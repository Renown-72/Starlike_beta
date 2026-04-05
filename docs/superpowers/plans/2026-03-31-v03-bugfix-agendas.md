# v0.3 Bug Fixes + Council Agendas Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fix 4 blocking/quality bugs and add 3 faction-specific council agendas to make the mod fully playable.

**Architecture:** Fix the broken anomaly trigger mechanism (HIGH), clean up duplicate modifiers (MEDIUM), sync version descriptors (LOW), clean up music file (LOW), then add council agendas gated by each faction's civic. All changes are Paradox Script (`.txt`) and YAML localization (`.yml`).

**Tech Stack:** Paradox Scripting Language, Stellaris v4.3, YAML localization

---

## File Map

| Action | File | Responsibility |
|--------|------|---------------|
| CREATE | `Starlike/common/anomalies/Starlike_anomalies.txt` | Anomaly definition for stellar-dust Earth |
| MODIFY | `Starlike/events/Starlike_homing_events.txt` | Fix event_homing.2 scope (planet_event -> ship_event), remove duplicate ambient object from event_homing.1 |
| MODIFY | `Starlike/common/governments/civics/Starlike_civics.txt` | Remove duplicate modifier keys |
| MODIFY | `Starlike/music/StarlikeOST.txt` | Remove misleading commented file paths |
| MODIFY | `Starlike/descriptor.mod` | Sync version to 4.3 |
| CREATE | `Starlike/common/council_agendas/Starlike_council_agendas.txt` | 3 faction agendas |
| MODIFY | `Starlike/localisation/l_english/Starlike_l_english.yml` | Add EN keys for anomaly + agendas |
| MODIFY | `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml` | Add CN keys for anomaly + agendas |

---

## Task 1: Create anomaly definition for Homing Earth

The core bug: `event_homing.2` has `is_triggered_only = yes` but nothing triggers it. We create an anomaly that spawns when surveying the `Homing_Earth_flag` planet and fires `event_homing.2` on success.

**Files:**
- Create: `Starlike/common/anomalies/Starlike_anomalies.txt`

- [ ] **Step 1: Create the anomalies directory**

Run:
```bash
mkdir -p "Starlike/common/anomalies"
```

- [ ] **Step 2: Create the anomaly definition file**

Create `Starlike/common/anomalies/Starlike_anomalies.txt`:

```pdx
# 星尘故土异常 — 在调查 Homing_Earth_flag 星球时自动生成
anomaly_homing_earth_stardust = {
    desc = anomaly_homing_earth_stardust_desc
    picture = GFX_evt_anomaly_overheat
    level = 2
    max_once_global = 1

    spawn_chance = {
        weight = 0
        modifier = {
            add = 100
            has_planet_flag = Homing_Earth_flag
        }
    }

    on_success = event_homing.2
}
```

Key design decisions:
- `level = 2`: Low scientist level requirement — this is a narrative event, not a challenge gate
- `max_once_global = 1`: Only spawns once per game
- `spawn_chance`: Weight 0 by default, +100 when the planet has `Homing_Earth_flag` — guaranteed spawn on the target planet, invisible everywhere else
- `on_success`: Fires `event_homing.2` (which we'll convert to `ship_event` in Task 2)

- [ ] **Step 3: Commit**

```bash
git add Starlike/common/anomalies/Starlike_anomalies.txt
git commit -m "feat: add anomaly definition for Homing Earth survey trigger"
```

---

## Task 2: Fix event_homing.2 scope and clean up event_homing.1

The anomaly `on_success` fires a `ship_event` (the science ship that completed the research). We must convert `event_homing.2` from `planet_event` to `ship_event` and adjust all scoping. Also remove the duplicate `create_ambient_object` from `event_homing.1`.

**Files:**
- Modify: `Starlike/events/Starlike_homing_events.txt`

- [ ] **Step 1: Remove duplicate ambient object from event_homing.1**

In `Starlike/events/Starlike_homing_events.txt`, remove lines 29-35 (the `create_ambient_object` block inside event_homing.1). The initializer `Homing_initializers.txt` already creates this ambient object on the planet's `init_effect`.

Before (lines 29-36):
```pdx
        # 首次开局时在恒星系发现异常，为探索叙事埋线
        every_system = {
            limit = { has_star_flag = Homing_Sol }
            create_ambient_object = {
                type = "anomaly_exoplanet_01_ambient_object"
            }
        }
```

After: Remove this entire block. The `immediate = { }` block of event_homing.1 should end after the Europa colonization `every_system` block (line 28's closing `}`).

- [ ] **Step 2: Convert event_homing.2 from planet_event to ship_event**

Replace the entire `event_homing.2` block with:

```pdx
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# event_homing.2  探索：星尘化地球
# 触发：科学船完成 anomaly_homing_earth_stardust 异常研究
# 作用域：this = 科学船, owner = 国家
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ship_event = {
    id = event_homing.2
    title = "event_homing.2.title"
    desc = "event_homing.2.desc"
    picture = GFX_evt_anomaly_overheat
    is_triggered_only = yes
    trigger = {
        owner = {
            NOT = { has_country_flag = homing_earth_investigating }
            NOT = { has_country_flag = homing_earth_analyzed }
        }
    }
    option = {
        name = "event_homing.2.opt.investigate"
        owner = {
            country_event = {
                id = event_homing.3
                days = 30
            }
            set_timed_country_flag = {
                flag = homing_earth_investigating
                days = 3650
            }
        }
    }
    option = {
        name = "event_homing.2.opt.defer"
        # 延迟探索，异常不会再次出现（max_once_global = 1）
    }
}
```

Changes from the original:
1. `planet_event` → `ship_event` (anomaly on_success fires in ship scope)
2. Removed broken `fromfrom = { has_planet_flag = Homing_Earth_flag }` trigger (the anomaly already ensures correct planet)
3. Added `owner = { NOT = { has_country_flag = ... } }` gates to prevent re-triggering
4. Wrapped effects in `owner = { }` scope (ship → country)
5. Changed `trigger_event` to `country_event` (explicit scope for country_event chaining)

- [ ] **Step 3: Commit**

```bash
git add Starlike/events/Starlike_homing_events.txt
git commit -m "fix: convert event_homing.2 to ship_event for anomaly integration"
```

---

## Task 3: Fix duplicate modifier keys in civics

Two civics define the same modifier key twice in their `modifier = { }` block. Paradox engine silently uses the last value.

**Files:**
- Modify: `Starlike/common/governments/civics/Starlike_civics.txt`

- [ ] **Step 1: Fix civic_huisu duplicate**

In `Starlike_civics.txt`, the `civic_huisu` modifier block (lines 13-22):

Before:
```pdx
    modifier = {
        # 博物传承：考古与遗珍收集
        category_archaeostudies_research_speed_mult = 0.25
        country_unity_produces_mult = 0.10
        # 治理效率：博物馆成员的行政传统
        country_unity_produces_mult = 0.15
        country_edict_fund_add = 10
        # 代价：重文轻武，舰船容量略低
        country_naval_cap_mult = -0.1
    }
```

After:
```pdx
    modifier = {
        # 博物传承：考古与遗珍收集
        category_archaeostudies_research_speed_mult = 0.25
        # 治理效率：博物馆成员的行政传统
        country_unity_produces_mult = 0.15
        country_edict_fund_add = 10
        # 代价：重文轻武，舰船容量略低
        country_naval_cap_mult = -0.1
    }
```

Remove the `country_unity_produces_mult = 0.10` line, keep the `0.15` line. Combined intent: one 15% unity bonus from the archival tradition.

- [ ] **Step 2: Fix civic_martis duplicate**

In `Starlike_civics.txt`, the `civic_martis` modifier block (lines 36-44):

Before:
```pdx
    modifier = {
        # 科学探索：荧惑派的科研优势
        all_technology_research_speed = 0.1
        science_ship_survey_speed = 0.2
        ship_anomaly_research_speed_mult = 0.2
        science_ship_survey_speed = 0.15
        # 代价：领袖池略小（科学治国，人才集中）
        country_leader_pool_size = -1
    }
```

After:
```pdx
    modifier = {
        # 科学探索：荧惑派的科研优势
        all_technology_research_speed = 0.1
        science_ship_survey_speed = 0.2
        ship_anomaly_research_speed_mult = 0.2
        # 代价：领袖池略小（科学治国，人才集中）
        country_leader_pool_size = -1
    }
```

Remove the duplicate `science_ship_survey_speed = 0.15` line, keep `0.2` (the higher value, aligning with Mars reconstruction science identity).

- [ ] **Step 3: Update civic effects localization**

Update `civic_martis_effects` in both YAML files to remove the now-deleted duplicate survey speed line.

In `Starlike_l_english.yml`, change:
```yaml
  civic_martis_effects: "§H+10%§! Research Speed\n§H+20%§! Survey Speed\n§H+20%§! Anomaly Research Speed\n§H+15%§! Science Ship Survey Speed\n§R-1§! Leader Pool Size"
```
To:
```yaml
  civic_martis_effects: "§H+10%§! Research Speed\n§H+20%§! Survey Speed\n§H+20%§! Anomaly Research Speed\n§R-1§! Leader Pool Size"
```

In `Starlike_l_simp_chinese.yml`, change:
```yaml
  civic_martis_effects: "§H+10%§! 科研速度\n§H+20%§! 勘测速度\n§H+20%§! 异常研究速度\n§H+15%§! 科学船勘测速度\n§R-1§! 领袖储备容量"
```
To:
```yaml
  civic_martis_effects: "§H+10%§! 科研速度\n§H+20%§! 勘测速度\n§H+20%§! 异常研究速度\n§R-1§! 领袖储备容量"
```

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/governments/civics/Starlike_civics.txt Starlike/localisation/l_english/Starlike_l_english.yml Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml
git commit -m "fix: remove duplicate modifier keys in civic_huisu and civic_martis"
```

---

## Task 4: Clean up music file and sync version

**Files:**
- Modify: `Starlike/music/StarlikeOST.txt`
- Modify: `Starlike/descriptor.mod`

- [ ] **Step 1: Rewrite StarlikeOST.txt**

**Important context:** The music is already functional via `StarlikeOST.asset`, which contains correct `music = { name file volume }` blocks for all 25 tracks. The `.txt` file's `song = { name }` entries reference these assets to add them to the playlist. The commented `# file = ...` lines are misleading dead code — uncommenting them would break syntax since they fall outside the `song = { }` blocks.

Replace entire contents of `Starlike/music/StarlikeOST.txt` with:

```pdx
# Arnaud Roy - Starlike Original Soundtrack Vol.1
song = { name = "Hui_Su_Name" }
song = { name = "Hinna_Name" }
song = { name = "Soltos_Name" }
song = { name = "Fog_Name" }
song = { name = "Dawn_On_Me_Name" }
song = { name = "Canossa_Expectations_Name" }
song = { name = "Light_Sea_Boundary_Name" }
song = { name = "The_Graham_Act_Name" }
song = { name = "Planet_Reclamation_Name" }
song = { name = "Wiedburt_Name" }
song = { name = "The_Hover_Name" }
song = { name = "Moonlight_Route_Name" }

# H-Pi - Starlike Original Soundtrack Vol.2
song = { name = "Dive_Into_The_Abyss_Name" }
song = { name = "Wind_Of_The_Earth_Name" }
song = { name = "Celestial_Rush_Name" }
song = { name = "March_On_The_Enemies_Of_The_SUPREME_Name" }
song = { name = "The_Sacrifice_Name" }
song = { name = "Praptro_Name" }
song = { name = "Shine_Like_Our_Fathers_Name" }
song = { name = "Core_s_Will_Name" }
song = { name = "Rule_The_Stars_Name" }
song = { name = "Blood_Rose_Impact_Name" }
song = { name = "Extinguish_Me_Name" }
song = { name = "In_The_Name_Of_The_Ocean_Name" }
song = { name = "Dimensionality_Reduction_Name" }
```

This removes the misleading commented `# file =` lines. Audio loading is handled by `StarlikeOST.asset`.

- [ ] **Step 2: Sync descriptor.mod version**

In `Starlike/descriptor.mod`, change line 1:

Before: `version="4.0"`
After: `version="4.3"`

- [ ] **Step 3: Commit**

```bash
git add Starlike/music/StarlikeOST.txt Starlike/descriptor.mod
git commit -m "fix: clean up music playlist file, sync descriptor.mod version to 4.3"
```

---

## Task 5: Create council agendas

Three faction-specific council agendas, each gated by the corresponding civic.

**Files:**
- Create: `Starlike/common/council_agendas/Starlike_council_agendas.txt`

- [ ] **Step 1: Create the council_agendas directory**

Run:
```bash
mkdir -p "Starlike/common/council_agendas"
```

- [ ] **Step 2: Create the council agendas file**

Create `Starlike/common/council_agendas/Starlike_council_agendas.txt`:

```pdx
# 归巢计划派系议程
# 每个议程通过 has_civic 绑定对应派系

# ── 辉夙议程 ── 博物归档（Huisu: Museum Restoration）
agenda_huisu = {
    agenda_cost = 2000
    potential = {
        owner = { has_civic = civic_huisu }
    }
    modifier = {
        category_archaeostudies_research_speed_mult = 0.15
        country_unity_produces_mult = 0.10
    }
}

# ── 荧惑议程 ── 科学先行（Martis: Scientific Vanguard）
agenda_martis = {
    agenda_cost = 2000
    potential = {
        owner = { has_civic = civic_martis }
    }
    modifier = {
        all_technology_research_speed = 0.05
        science_ship_survey_speed = 0.15
    }
}

# ── 鸾羽议程 ── 军备扩张（Phoenix Plume: Military Expansion）
agenda_phoenix_plume = {
    agenda_cost = 2000
    potential = {
        owner = { has_civic = civic_phoenix_plume }
    }
    modifier = {
        country_naval_cap_mult = 0.10
        army_damage_mult = 0.10
    }
}
```

Design notes:
- `agenda_cost = 2000`: Unity cost to enact the agenda (lower than the reference's 3000 since these are faction-specific, not general)
- No `finish_modifier`: These agendas provide ongoing benefits, no special completion bonus
- No `allow` block: Available whenever the potential condition is met

- [ ] **Step 3: Commit**

```bash
git add Starlike/common/council_agendas/Starlike_council_agendas.txt
git commit -m "feat: add 3 faction-specific council agendas"
```

---

## Task 6: Add all localization keys

All new content needs EN + CN localization keys.

**Files:**
- Modify: `Starlike/localisation/l_english/Starlike_l_english.yml`
- Modify: `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

- [ ] **Step 1: Add English localization keys**

Append the following to `Starlike/localisation/l_english/Starlike_l_english.yml` (before the end of file):

```yaml

  # Anomaly — Homing Earth stardust
  anomaly_homing_earth_stardust_desc: "This planet — once called Earth — has been transmuted into stellar dust. Yet faint energy signatures pulse from within, as though the planet remembers what it was."

  # Council Agendas
  council_agenda_agenda_huisu_name: "Museum Restoration Initiative"
  council_agenda_agenda_huisu_desc: "Prioritize archival recovery and cultural heritage preservation. Focus resources on archaeological research and unity-building through shared history."
  council_agenda_agenda_martis_name: "Scientific Vanguard Protocol"
  council_agenda_agenda_martis_desc: "Accelerate all research programs and survey operations. Channel the scientific legacy of Mars reconstruction into systematic galactic exploration."
  council_agenda_agenda_phoenix_plume_name: "Military Expansion Directive"
  council_agenda_agenda_phoenix_plume_desc: "Expand naval capacity and strengthen ground forces. The Phoenix Plume doctrine demands readiness against all threats to the Commonwealth."
```

- [ ] **Step 2: Add Chinese localization keys**

Append the following to `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml` (before the end of file):

```yaml

  # 异常 — 归巢地球星尘
  anomaly_homing_earth_stardust_desc: "这颗曾被称为"地球"的星球，已化作星尘。然而，微弱的能量信号从中脉动，仿佛星球仍记得它曾经的模样。"

  # 内阁议程
  council_agenda_agenda_huisu_name: "博物归档计划"
  council_agenda_agenda_huisu_desc: "优先进行档案回收与文化遗产保护。将资源集中于考古研究和通过共同历史凝聚人心。"
  council_agenda_agenda_martis_name: "科学先行议定"
  council_agenda_agenda_martis_desc: "加速所有科研项目和勘测行动。将火星重建时期的科学遗产转化为系统性的星系探索。"
  council_agenda_agenda_phoenix_plume_name: "军备扩张令"
  council_agenda_agenda_phoenix_plume_desc: "扩充舰队容量，强化地面部队。鸾羽军令要求我们对一切威胁保持戒备。"
```

- [ ] **Step 3: Commit**

```bash
git add Starlike/localisation/l_english/Starlike_l_english.yml Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml
git commit -m "feat: add localization for anomaly and council agendas (EN + CN)"
```

---

## Task 7: Verify and final commit

- [ ] **Step 1: Verify all new files exist**

Run:
```bash
ls Starlike/common/anomalies/Starlike_anomalies.txt
ls Starlike/common/council_agendas/Starlike_council_agendas.txt
```

Expected: Both files listed.

- [ ] **Step 2: Verify no syntax issues in modified files**

Run:
```bash
grep -c "country_unity_produces_mult" Starlike/common/governments/civics/Starlike_civics.txt
```

Expected: `2` (one in civic_huisu at 0.15, one in civic_phoenix_plume at -0.05). If `3`, the duplicate wasn't removed.

Run:
```bash
grep -c "science_ship_survey_speed" Starlike/common/governments/civics/Starlike_civics.txt
```

Expected: `1` (only the 0.2 line in civic_martis). If `2`, the duplicate wasn't removed.

- [ ] **Step 3: Verify event_homing.2 is now ship_event**

Run:
```bash
grep "ship_event" Starlike/events/Starlike_homing_events.txt
```

Expected: One line containing `ship_event = {`.

Run:
```bash
grep "planet_event" Starlike/events/Starlike_homing_events.txt
```

Expected: No output (event_homing.2 was the only planet_event, now converted).

- [ ] **Step 4: Verify descriptor.mod version**

Run:
```bash
grep "version" Starlike/descriptor.mod
```

Expected: `version="4.3"`

- [ ] **Step 5: Verify localization key count**

Run:
```bash
grep -c "agenda" Starlike/localisation/l_english/Starlike_l_english.yml
```

Expected: `6` (3 name + 3 desc keys).

Run:
```bash
grep -c "anomaly_homing" Starlike/localisation/l_english/Starlike_l_english.yml
```

Expected: `1`.

- [ ] **Step 6: In-game verification steps (manual)**

After loading the mod in Stellaris:

1. **Anomaly test**: Start a new game with origin_homing. Send a science ship to survey the stellar-dust Earth. An anomaly should appear. Research it. `event_homing.2` should fire as a popup.

2. **Event chain test**: Choose "Investigate" in event_homing.2. After 30 days, event_homing.3 should fire. Test both options (archive and decode paths).

3. **Council agenda test**: Open the council screen. With civic_huisu, `agenda_huisu` should be available. Same for the other two factions.

4. **Music test**: Check the in-game music player — all 25 tracks should appear.

5. **Error log**: Check `%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log` for any mod-related errors.

Console shortcuts for testing:
```
event event_homing.2          # Direct-fire the anomaly event (bypass anomaly)
event event_homing.3          # Test investigation completion
event event_homing.4          # Test legacy unlock
```
