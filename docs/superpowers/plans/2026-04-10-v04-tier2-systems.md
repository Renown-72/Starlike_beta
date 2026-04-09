# v0.4 Tier 2 Systems Supplement Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Supplement v0.4 with missing gameplay systems — 3 faction buildings, 3 jobs, 2 decisions, 1 edict, 1 army, 3 event chains, 6 game concepts, 1 start screen message, 2 message types, and scripted infrastructure.

**Architecture:** All new systems gate on `origin_homing`. Buildings gate on tradition tree adoption (civic → tradition → building). New files follow `sl_` prefix convention. Localization is bilingual (EN + CN), UTF-8 BOM.

**Tech Stack:** Paradox Script (.txt), Stellaris v4.3 DSL, UTF-8 BOM YAML localization.

---

## File Structure

| File | Action | Responsibility |
|------|--------|----------------|
| `Starlike/common/scripted_triggers/sl_triggers.txt` | Create | 3 reusable triggers |
| `Starlike/common/scripted_effects/sl_effects.txt` | Create | 3 event chain progress helpers |
| `Starlike/common/buildings/sl_buildings.txt` | Create | 3 faction empire buildings |
| `Starlike/common/pop_jobs/sl_jobs.txt` | Create | 3 specialist jobs |
| `Starlike/common/decisions/sl_decisions.txt` | Create | 2 decisions |
| `Starlike/common/edicts/sl_edicts.txt` | Create | 1 edict |
| `Starlike/common/static_modifiers/sl_modifiers.txt` | Modify | +2 modifiers (append) |
| `Starlike/common/armies/sl_armies.txt` | Create | 1 army type |
| `Starlike/common/event_chains/sl_event_chains.txt` | Create | 3 event chain trackers |
| `Starlike/common/message_types/sl_messages.txt` | Create | 2 message types |
| `Starlike/common/game_concepts/sl_concepts.txt` | Create | 6 game concepts |
| `Starlike/common/start_screen_messages/sl_start_screen.txt` | Create | 1 start screen part |
| `Starlike/events/sl_homing_events.txt` | Modify | +event_homing.17, +chain calls in events 1-16 |
| `Starlike/localisation/l_english/sl_l_english.yml` | Modify | +~52 keys (append) |
| `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml` | Modify | +~52 keys (append) |

---

## Task 1: Create Scripted Triggers

**Files:**
- Create: `Starlike/common/scripted_triggers/sl_triggers.txt`

- [ ] **Step 1: Create directory**

Run: `mkdir -p Starlike/common/scripted_triggers`

- [ ] **Step 2: Write sl_triggers.txt**

```plaintext
# ===== Starlike Scripted Triggers =====

# 归巢起源检查
sl_is_homing_origin = {
    has_origin = origin_homing
}

# 任一派系传统树完成
sl_has_any_faction_tradition = {
    OR = {
        has_tradition = tr_huisu_finish
        has_tradition = tr_martis_finish
        has_tradition = tr_phoenix_finish
    }
}

# 任一派系飞升已点
sl_has_any_faction_ascendant = {
    OR = {
        has_ascension_perk = ap_huisu_ascendant
        has_ascension_perk = ap_martis_ascendant
        has_ascension_perk = ap_phoenix_ascendant
    }
}
```

- [ ] **Step 3: Verify file**

Run: `cat Starlike/common/scripted_triggers/sl_triggers.txt | head -5`
Expected: First lines show `# ===== Starlike Scripted Triggers =====` and `sl_is_homing_origin`

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/scripted_triggers/sl_triggers.txt
git commit -m "feat: add scripted triggers (sl_is_homing_origin, sl_has_any_faction_tradition, sl_has_any_faction_ascendant)"
```

---

## Task 2: Create Scripted Effects

**Files:**
- Create: `Starlike/common/scripted_effects/sl_effects.txt`

- [ ] **Step 1: Create directory**

Run: `mkdir -p Starlike/common/scripted_effects`

- [ ] **Step 2: Write sl_effects.txt**

```plaintext
# ===== Starlike Scripted Effects =====

# 安全推进地球遗产事件链
sl_progress_earth_chain = {
    if = {
        limit = { has_event_chain = sl_chain_earth_legacy }
        add_event_chain_counter = {
            event_chain = sl_chain_earth_legacy
            counter = sl_earth_legacy_progress
            amount = 1
        }
    }
}

# 安全推进派系叙事事件链
sl_progress_faction_chain = {
    if = {
        limit = { has_event_chain = sl_chain_faction_story }
        add_event_chain_counter = {
            event_chain = sl_chain_faction_story
            counter = sl_faction_story_progress
            amount = 1
        }
    }
}

# 安全推进汇聚事件链
sl_progress_convergence_chain = {
    if = {
        limit = { has_event_chain = sl_chain_convergence }
        add_event_chain_counter = {
            event_chain = sl_chain_convergence
            counter = sl_convergence_progress
            amount = 1
        }
    }
}
```

- [ ] **Step 3: Commit**

```bash
git add Starlike/common/scripted_effects/sl_effects.txt
git commit -m "feat: add scripted effects for event chain progress tracking"
```

---

## Task 3: Create 3 Faction Buildings

**Files:**
- Create: `Starlike/common/buildings/sl_buildings.txt`

- [ ] **Step 1: Create directory**

Run: `mkdir -p Starlike/common/buildings`

- [ ] **Step 2: Write sl_buildings.txt with all 3 buildings**

```plaintext
# ===== Starlike v0.4 Tier 2 — Faction Empire Buildings =====
# 每个派系一个帝国唯一建筑，由传统树采纳解锁
# 链路：civic → tradition adopt → building visible

# ── 辉夙文明殿 ──
building_sl_huisu_hall = {
    base_buildtime = 480
    category = unity

    empire_limit = { base = 1 }

    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_huisu_adopt
        }
    }

    allow = {
        has_major_capital = yes
    }

    planet_modifier = {
        pop_happiness = 0.05
    }

    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_huisu_adopt }
        }
        modifier = {
            job_sl_archivist_add = 3
        }
    }

    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            unity = 200
        }
        upkeep = {
            energy = 3
        }
    }

    prerequisites = { }

    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_huisu_adopt } }
        }
    }
}

# ── 荧惑研究所 ──
building_sl_martis_institute = {
    base_buildtime = 480
    category = research

    empire_limit = { base = 1 }

    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_martis_adopt
        }
    }

    allow = {
        has_major_capital = yes
    }

    planet_modifier = {
        planet_researchers_physics_research_produces_add = 2
    }

    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_martis_adopt }
        }
        modifier = {
            job_sl_researcher_add = 3
        }
    }

    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            alloys = 100
        }
        upkeep = {
            energy = 4
        }
    }

    prerequisites = { }

    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_martis_adopt } }
        }
    }
}

# ── 鸾羽军事堡垒 ──
building_sl_phoenix_fortress = {
    base_buildtime = 480
    category = army

    empire_limit = { base = 1 }

    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_phoenix_adopt
        }
    }

    allow = {
        has_major_capital = yes
    }

    country_modifier = {
        country_naval_cap_add = 20
    }

    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_phoenix_adopt }
        }
        modifier = {
            job_sl_quartermaster_add = 3
        }
    }

    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            alloys = 150
        }
        upkeep = {
            energy = 3
            alloys = 1
        }
    }

    prerequisites = { }

    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_phoenix_adopt } }
        }
    }
}
```

- [ ] **Step 3: Verify 3 building definitions exist**

Run: `grep -c "^building_sl_" Starlike/common/buildings/sl_buildings.txt`
Expected: `3`

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/buildings/sl_buildings.txt
git commit -m "feat: add 3 faction empire buildings (huisu_hall, martis_institute, phoenix_fortress)

- All empire_limit = 1, capital-only
- Gated by tradition tree adoption (tr_*_adopt)
- Each provides 3 specialist jobs"
```

---

## Task 4: Create 3 Specialist Jobs

**Files:**
- Create: `Starlike/common/pop_jobs/sl_jobs.txt`

- [ ] **Step 1: Create directory**

Run: `mkdir -p Starlike/common/pop_jobs`

- [ ] **Step 2: Write sl_jobs.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Faction Jobs =====

# ── 档案官（辉夙文明殿） ──
sl_archivist = {
    category = specialist
    building_icon = building_sl_huisu_hall
    clothes_texture_index = 3

    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }

    possible_precalc = can_fill_specialist_job

    resources = {
        category = planet_jobs
        produces = {
            unity = 4
            society_research = 3
        }
    }

    triggered_planet_modifier = {
        potential = { always = yes }
        planet_amenities_add = 3
    }

    weight = {
        weight = @specialist_job_weight
    }
}

# ── 荧惑研究员（荧惑研究所） ──
sl_researcher = {
    category = specialist
    building_icon = building_sl_martis_institute
    clothes_texture_index = 3

    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }

    possible_precalc = can_fill_specialist_job

    resources = {
        category = planet_jobs
        produces = {
            physics_research = 4
            engineering_research = 3
        }
    }

    weight = {
        weight = @specialist_job_weight
    }
}

# ── 军需官（鸾羽军事堡垒） ──
sl_quartermaster = {
    category = specialist
    building_icon = building_sl_phoenix_fortress
    clothes_texture_index = 3

    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }

    possible_precalc = can_fill_specialist_job

    resources = {
        category = planet_jobs
        produces = {
            alloys = 2
        }
    }

    country_modifier = {
        country_naval_cap_add = 4
    }

    weight = {
        weight = @specialist_job_weight
    }
}
```

- [ ] **Step 3: Verify 3 job definitions**

Run: `grep -c "^sl_" Starlike/common/pop_jobs/sl_jobs.txt`
Expected: `3`

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/pop_jobs/sl_jobs.txt
git commit -m "feat: add 3 specialist jobs (sl_archivist, sl_researcher, sl_quartermaster)"
```

---

## Task 5: Create Decisions + Edict + New Modifiers

**Files:**
- Create: `Starlike/common/decisions/sl_decisions.txt`
- Create: `Starlike/common/edicts/sl_edicts.txt`
- Modify: `Starlike/common/static_modifiers/sl_modifiers.txt`

- [ ] **Step 1: Create directories**

Run: `mkdir -p Starlike/common/decisions Starlike/common/edicts`

- [ ] **Step 2: Write sl_decisions.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Decisions =====

# ── 重建地球 ──
decision_sl_rebuild_earth = {
    owned_planets_only = yes

    resources = {
        category = decisions
        cost = {
            unity = 2000
            alloys = 500
        }
    }

    enactment_time = 720

    potential = {
        has_planet_flag = Homing_Earth_flag
        owner = {
            has_origin = origin_homing
            has_ascension_perk = ap_ranshanxia_bloodline
            has_country_flag = homing_earth_legacy_unlocked
        }
        NOT = { has_planet_flag = sl_earth_rebuilt }
    }

    allow = {
        custom_tooltip = {
            fail_text = "decision_sl_rebuild_earth_requirement"
            owner = { has_ascension_perk = ap_ranshanxia_bloodline }
        }
    }

    effect = {
        set_planet_flag = sl_earth_rebuilt
        change_pc = pc_continental
        add_modifier = { modifier = modifier_sl_rebuilt_earth days = -1 }
        owner = {
            country_event = { id = event_homing.17 days = 5 }
        }
    }

    ai_weight = { weight = 0 }
}

# ── 归巢广播 ──
decision_sl_homing_broadcast = {
    owned_planets_only = yes

    resources = {
        category = decisions
        cost = {
            unity = 500
        }
    }

    enactment_time = 180

    potential = {
        owner = {
            has_origin = origin_homing
            sl_has_any_faction_tradition = yes
        }
        NOT = { has_modifier = sl_broadcast_active }
    }

    effect = {
        add_modifier = { modifier = sl_broadcast_active days = 3600 }
    }

    ai_weight = {
        weight = 5
        modifier = {
            factor = 0
            has_modifier = sl_broadcast_active
        }
    }
}
```

- [ ] **Step 3: Write sl_edicts.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Edicts =====

# ── 归巢令 ──
edict_sl_homing_recall = {
    length = -1

    potential = {
        has_origin = origin_homing
        sl_has_any_faction_tradition = yes
    }

    allow = { }

    resources = {
        category = edicts
        upkeep = {
            unity = 10
        }
    }

    modifier = {
        country_unity_produces_mult = 0.05
        leader_lifespan_add = 10
        pop_happiness = 0.05
    }

    ai_weight = {
        weight = 5
        modifier = {
            factor = 0
            NOT = { has_origin = origin_homing }
        }
    }
}
```

- [ ] **Step 4: Append 2 modifiers to sl_modifiers.txt**

Append to end of `Starlike/common/static_modifiers/sl_modifiers.txt`:

```plaintext

# ===== Tier 2 Modifiers · v0.4 补全 =====

# 重建地球永久修正
modifier_sl_rebuilt_earth = {
    icon = "GFX_modifier_research_speed"
    planet_jobs_produces_mult = 0.20
}

# 归巢广播临时修正
sl_broadcast_active = {
    icon = "GFX_modifier_pop_happiness"
    planet_immigration_pull_mult = 0.25
    pop_happiness = 0.05
}
```

- [ ] **Step 5: Verify modifier count**

Run: `grep -c "= {" Starlike/common/static_modifiers/sl_modifiers.txt`
Expected: `9` (7 existing + 2 new)

- [ ] **Step 6: Commit**

```bash
git add Starlike/common/decisions/sl_decisions.txt Starlike/common/edicts/sl_edicts.txt Starlike/common/static_modifiers/sl_modifiers.txt
git commit -m "feat: add 2 decisions (rebuild_earth, homing_broadcast) + 1 edict (homing_recall) + 2 modifiers"
```

---

## Task 6: Create Army Type

**Files:**
- Create: `Starlike/common/armies/sl_armies.txt`

- [ ] **Step 1: Create directory**

Run: `mkdir -p Starlike/common/armies`

- [ ] **Step 2: Write sl_armies.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Armies =====

# ── 归巢志愿军 ──
sl_homing_volunteer = {
    damage = 1.50
    health = 1.20
    morale = 2.00
    collateral_damage = 0.75
    war_exhaustion = 0.80
    time = 90

    icon = GFX_army_type_defense

    potential_country = {
        has_origin = origin_homing
    }

    potential = {
        from = { is_sapient = yes }
    }

    resources = {
        category = armies
        cost = {
            minerals = 75
        }
        upkeep = {
            food = 1
        }
    }

    ai_weight = {
        base = 5
        modifier = {
            factor = 0
            NOT = { owner = { has_origin = origin_homing } }
        }
    }
}
```

- [ ] **Step 3: Commit**

```bash
git add Starlike/common/armies/sl_armies.txt
git commit -m "feat: add sl_homing_volunteer army (high morale, low collateral)"
```

---

## Task 7: Create Event Chains + Message Types

**Files:**
- Create: `Starlike/common/event_chains/sl_event_chains.txt`
- Create: `Starlike/common/message_types/sl_messages.txt`

- [ ] **Step 1: Create directories**

Run: `mkdir -p Starlike/common/event_chains Starlike/common/message_types`

- [ ] **Step 2: Write sl_event_chains.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Event Chain Trackers =====

# 地球遗产链 (events 1-4)
sl_chain_earth_legacy = {
    icon = "gfx/interface/icons/situation_log/situation_log_quest.dds"
    picture = GFX_evt_anomaly_overheat

    counter = {
        sl_earth_legacy_progress = { max = 4 }
    }

    abort_trigger = {
        has_country_flag = homing_earth_legacy_unlocked
    }
}

# 派系叙事链 (events 5-13)
sl_chain_faction_story = {
    icon = "gfx/interface/icons/situation_log/situation_log_quest.dds"
    picture = GFX_evt_metropolis

    counter = {
        sl_faction_story_progress = { max = 3 }
    }

    abort_trigger = {
        OR = {
            AND = { has_country_flag = event_homing_5_fired has_country_flag = huisu_heritage_resolved }
            AND = { has_country_flag = event_homing_8_fired has_country_flag = martis_boundary_pushed }
            AND = { has_country_flag = event_homing_11_fired has_country_flag = luanyu_return_promised }
        }
    }
}

# 汇聚叙事链 (events 14-16)
sl_chain_convergence = {
    icon = "gfx/interface/icons/situation_log/situation_log_precursor.dds"
    picture = GFX_evt_planet_settlement

    counter = {
        sl_convergence_progress = { max = 3 }
    }

    abort_trigger = {
        has_country_flag = homing_convergence_complete
    }
}
```

- [ ] **Step 3: Write sl_messages.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Message Types =====

message_type = {
    key = MESSAGE_SL_EVENT_PROGRESS
    icon = GFX_message_quest
    icon_frame = 1
    name = MESSAGE_SL_EVENT_PROGRESS
    message_setting_key = MESSAGE_SL_EVENT_PROGRESS.s
    sound = notification
    ping = ping_notification_green
    category = science
}

message_type = {
    key = MESSAGE_SL_HOMING_MILESTONE
    icon = GFX_message_quest
    icon_frame = 1
    name = MESSAGE_SL_HOMING_MILESTONE
    message_setting_key = MESSAGE_SL_HOMING_MILESTONE.s
    sound = event_notification
    ping = ping_notification_yellow
    category = unity
}
```

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/event_chains/sl_event_chains.txt Starlike/common/message_types/sl_messages.txt
git commit -m "feat: add 3 event chain trackers + 2 message types for situation log"
```

---

## Task 8: Create Game Concepts + Start Screen

**Files:**
- Create: `Starlike/common/game_concepts/sl_concepts.txt`
- Create: `Starlike/common/start_screen_messages/sl_start_screen.txt`

- [ ] **Step 1: Create directories**

Run: `mkdir -p Starlike/common/game_concepts Starlike/common/start_screen_messages`

- [ ] **Step 2: Write sl_concepts.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Game Concepts =====

concept_sl_origin_homing = {
    icon = gfx/interface/icons/concepts/concept_origins.dds
}
concept_sl_huisu = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_martis = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_phoenix = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_ranshanxia = {
    icon = gfx/interface/icons/concepts/concept_trait.dds
}
concept_sl_homing_tradition = {
    icon = gfx/interface/icons/concepts/concept_traditions.dds
}
```

- [ ] **Step 3: Write sl_start_screen.txt**

```plaintext
# ===== Starlike v0.4 Tier 2 — Start Screen =====

part = {
    location = 0
    localization = origins_sl_homing_start_screen
    trigger = {
        has_origin = origin_homing
    }
}
```

- [ ] **Step 4: Commit**

```bash
git add Starlike/common/game_concepts/sl_concepts.txt Starlike/common/start_screen_messages/sl_start_screen.txt
git commit -m "feat: add 6 game concepts + start screen message for origin_homing"
```

---

## Task 9: Add event_homing.17 + Chain Calls to Existing Events

**Files:**
- Modify: `Starlike/events/sl_homing_events.txt`

This is the most complex task. We need to:
1. Append event_homing.17 at the end
2. Inject `begin_event_chain` into event_homing.1 (start of earth legacy chain)
3. Inject `sl_progress_earth_chain` calls into events 2, 3, 4
4. Inject `begin_event_chain` for faction chain into events 5, 8, 11
5. Inject `sl_progress_faction_chain` calls into events 6/7, 9/10, 12/13
6. Inject `begin_event_chain` for convergence chain into event 14
7. Inject `sl_progress_convergence_chain` calls into events 15, 16

- [ ] **Step 1: Append event_homing.17 to end of sl_homing_events.txt**

Append after line 451 (after the closing `}` of event_homing.16):

```plaintext

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# event_homing.17  重建地球庆典
# 触发：decision_sl_rebuild_earth 完成后 5 天
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
country_event = {
    id = event_homing.17
    title = "event_homing.17.title"
    desc = "event_homing.17.desc"
    picture = GFX_evt_planet_settlement
    is_triggered_only = yes
    immediate = { }
    option = {
        name = "event_homing.17.opt.new_dawn"
        add_resource = { type = unity amount = 1000 }
        add_resource = { type = influence amount = 200 }
    }
}
```

- [ ] **Step 2: Inject earth legacy chain start into event_homing.1**

In event_homing.1, inside the `immediate` block (after line 13), add at the top of immediate:

```plaintext
        # 开始地球遗产事件链追踪
        begin_event_chain = {
            event_chain = sl_chain_earth_legacy
        }
        sl_progress_earth_chain = yes
```

- [ ] **Step 3: Inject chain progress into event_homing.2**

In event_homing.2 (line 37), in the first option block (the "investigate" option, around line 49-61), add inside the option before the closing `}`:

```plaintext
        owner = { sl_progress_earth_chain = yes }
```

- [ ] **Step 4: Inject chain progress into event_homing.3**

In event_homing.3 (line 72), in the `immediate` block (line 82-84), add after `set_country_flag = homing_earth_analyzed`:

```plaintext
        sl_progress_earth_chain = yes
```

- [ ] **Step 5: Inject chain progress into event_homing.4**

In event_homing.4 (line 116), in the `immediate` block (line 127-128), add after `set_country_flag = homing_earth_legacy_unlocked`:

```plaintext
        sl_progress_earth_chain = yes
```

- [ ] **Step 6: Inject faction chain start into events 5, 8, 11**

In event_homing.5 `immediate` block, add:

```plaintext
        begin_event_chain = {
            event_chain = sl_chain_faction_story
        }
        sl_progress_faction_chain = yes
```

In event_homing.8 `immediate` block, add the same (begin_event_chain + sl_progress_faction_chain).

In event_homing.11 `immediate` block, add the same.

- [ ] **Step 7: Inject faction chain progress into events 6, 7, 9, 10, 12, 13**

In each of these events' `immediate` block, add:

```plaintext
        sl_progress_faction_chain = yes
```

For events that don't have an `immediate` block (events 7, 10, 13), add one:

```plaintext
    immediate = {
        sl_progress_faction_chain = yes
    }
```

- [ ] **Step 8: Inject convergence chain start into event_homing.14**

In event_homing.14 `immediate` block, add:

```plaintext
        begin_event_chain = {
            event_chain = sl_chain_convergence
        }
        sl_progress_convergence_chain = yes
```

- [ ] **Step 9: Inject convergence progress into events 15, 16**

In event_homing.15 `immediate` block, add:

```plaintext
        sl_progress_convergence_chain = yes
```

In event_homing.16 `immediate` block (line 443-445), add after `set_country_flag`:

```plaintext
        sl_progress_convergence_chain = yes
```

- [ ] **Step 10: Verify event counts**

Run: `grep -c "id = event_homing" Starlike/events/sl_homing_events.txt`
Expected: `17` (16 existing + 1 new, counting trigger_event refs too, so check unique IDs)

Run: `grep -c "sl_progress_" Starlike/events/sl_homing_events.txt`
Expected: At least `13` (earth: 4, faction: 9 events across 3 chains, convergence: 3)

- [ ] **Step 11: Commit**

```bash
git add Starlike/events/sl_homing_events.txt
git commit -m "feat: add event_homing.17 (earth rebuild) + wire event chain tracking into events 1-16"
```

---

## Task 10: Add All Bilingual Localization

**Files:**
- Modify: `Starlike/localisation/l_english/sl_l_english.yml` (append after line 233)
- Modify: `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml` (append after last line)

**IMPORTANT:** Both files are UTF-8 with BOM. Use append mode, do NOT rewrite the file.

- [ ] **Step 1: Append English localization**

Append to `Starlike/localisation/l_english/sl_l_english.yml`:

```yaml

  # ===== Tier 2 Systems · v0.4 =====

  # Buildings
  building_sl_huisu_hall: "Huisu Hall of Civilization"
  building_sl_huisu_hall_desc: "The crown jewel of the Huisu faction — a grand archive preserving every record from before and after the Sol cataclysm. Its archivists safeguard the collective memory of COPAN civilization."
  building_sl_martis_institute: "Martis Research Institute"
  building_sl_martis_institute_desc: "The pinnacle of Martis scientific tradition — a research hub continuing the cutting-edge experiments begun during the Mars reconstruction era."
  building_sl_phoenix_fortress: "Luanyu War Citadel"
  building_sl_phoenix_fortress_desc: "The heart of the Luanyu military command — a fortress embodying the spirit of the original Phoenix Plume warship that guarded the last evacuation from Sol."

  # Jobs
  sl_archivist: "Archivist"
  sl_archivist_desc: "Archivists preserve and interpret the records of COPAN civilization, generating unity through cultural continuity and advancing social research."
  sl_researcher: "Martis Researcher"
  sl_researcher_desc: "Martis Researchers carry the flame of Mars-era science, pushing boundaries in physics and engineering."
  sl_quartermaster: "Quartermaster"
  sl_quartermaster_desc: "Quartermasters manage the Luanyu fleet's logistics, producing alloys and expanding naval capacity."

  # Decisions
  decision_sl_rebuild_earth: "Rebuild Earth"
  decision_sl_rebuild_earth_desc: "The ultimate reward of Project Homing — transform the stellar-dust Earth back into a habitable world."
  decision_sl_rebuild_earth_requirement: "Requires the Ranshanxia Bloodline ascension perk."
  decision_sl_homing_broadcast: "Homing Broadcast"
  decision_sl_homing_broadcast_desc: "Broadcast the story of the Homing civilization across the galaxy, attracting those who share our vision."

  # Edict
  edict_sl_homing_recall: "Homing Recall"
  edict_sl_homing_recall_desc: "A national decree calling all citizens to remember the path of Homing, strengthening cultural identity and extending leader lifespans."

  # Army
  sl_homing_volunteer: "Homing Volunteer"
  sl_homing_volunteer_desc: "Citizens driven by the shared dream of returning home. They fight not with heavy technology, but with conviction and morale."
  sl_homing_volunteer_plural: "Homing Volunteers"

  # Event Chains
  sl_chain_earth_legacy: "Legacy of Earth"
  sl_chain_earth_legacy_desc: "Uncover the secrets of the stellar-dust Earth and reclaim the heritage of Sol."
  sl_chain_faction_story: "Faction Chronicles"
  sl_chain_faction_story_desc: "The story of your chosen faction unfolds as the civilization matures."
  sl_chain_convergence: "The Convergence"
  sl_chain_convergence_desc: "The three bloodlines of COPAN civilization converge toward a shared destiny."

  # Game Concepts
  concept_sl_origin_homing: "Project Homing"
  concept_sl_origin_homing_desc: "Project Homing is the origin story of the COPAN civilization — survivors of the Sol cataclysm who rebuilt on Mars and now seek to return to their ancestral home among the stars. Three factions guide their journey: Huisu (heritage), Martis (science), and Luanyu (military)."
  concept_sl_huisu: "Huisu Faction"
  concept_sl_huisu_desc: "The Museum Heritage faction. Huisu archivists preserve the collective memory of COPAN civilization, drawing strength from the past to guide the future. Their tradition tree focuses on unity, archaeology, and administrative efficiency."
  concept_sl_martis: "Martis Faction"
  concept_sl_martis_desc: "The Fervor Science faction. Martis researchers carry the flame of Mars-era experimentation, pushing the boundaries of physics and engineering. Their tradition tree focuses on research speed, anomaly analysis, and scientific exploration."
  concept_sl_phoenix: "Luanyu Faction"
  concept_sl_phoenix_desc: "The Phoenix Plume military faction. Luanyu commanders honor the iron discipline of the original evacuation fleet. Their tradition tree focuses on naval expansion, weapon systems, and ground combat superiority."
  concept_sl_ranshanxia: "Ranshanxia Bloodline"
  concept_sl_ranshanxia_desc: "The Ranshanxia Bloodline is the convergence of all three COPAN faction traditions. Named after the atmospheric phenomenon that once warmed the skies of Old Earth, it represents the unity of heritage, science, and strength."
  concept_sl_homing_tradition: "Homing Traditions"
  concept_sl_homing_tradition_desc: "Each COPAN faction has its own tradition tree, unlocked by the faction's civic. Completing a tradition tree unlocks the corresponding faction ascension perk and a unique empire building."

  # Start Screen
  origins_sl_homing_start_screen: "§HProject Homing§!\n\nA thousand years ago, the Sol system was consumed by stellar fire. The survivors — engineers, scholars, soldiers — fled to Mars and rebuilt. They called it Project Homing: the promise that one day, they would find their way back.\n\nNow, three factions guide the COPAN civilization: §YHuisu§!, keepers of memory; §YMartis§!, bearers of the scientific flame; and §YLuanyu§!, guardians of the iron fleet.\n\nThe stars await. The journey home begins."

  # Message Types
  MESSAGE_SL_EVENT_PROGRESS: "Homing Progress"
  MESSAGE_SL_EVENT_PROGRESS.s: "Homing Event Progress"
  MESSAGE_SL_HOMING_MILESTONE: "Homing Milestone"
  MESSAGE_SL_HOMING_MILESTONE.s: "Homing Milestones"

  # Tier 2 Modifiers
  modifier_sl_rebuilt_earth: "Rebuilt Earth"
  modifier_sl_rebuilt_earth_desc: "The ancestral home of humanity has been restored. Its renewed surface teems with life and industry."
  sl_broadcast_active: "Homing Broadcast"
  sl_broadcast_active_desc: "Our story echoes across the galaxy, drawing kindred spirits to our colonies."

  # Event 17
  event_homing.17.title: "A New Dawn"
  event_homing.17.desc: "It is done. The stellar dust has cleared, oceans have returned, and the sky is blue once more. Earth — not as it was, but as it could be — stands reborn beneath the stars. A thousand years of exile end not with a homecoming, but with a creation. We have not returned to the old world. We have built a new one."
  event_homing.17.opt.new_dawn: "Welcome home."
```

- [ ] **Step 2: Append Chinese localization**

Append to `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml`:

```yaml

  # ===== Tier 2 系统 · v0.4 =====

  # 建筑
  building_sl_huisu_hall: "辉夙文明殿"
  building_sl_huisu_hall_desc: "辉夙派的王冠明珠——一座宏伟的档案殿堂，保存着太阳系灾变前后的每一份记录。其档案官守护着汉和文明的集体记忆。"
  building_sl_martis_institute: "荧惑研究所"
  building_sl_martis_institute_desc: "荧惑科学传统的巅峰——延续火星重建时期尖端实验的帝国级研究枢纽。"
  building_sl_phoenix_fortress: "鸾羽军事堡垒"
  building_sl_phoenix_fortress_desc: "鸾羽军事指挥的核心——体现凤翎号战舰精神的帝国级要塞，守护着归巢的最后撤离。"

  # 岗位
  sl_archivist: "档案官"
  sl_archivist_desc: "档案官保存并诠释汉和文明的记录，通过文化传承产生凝聚力，并推进社会学研究。"
  sl_researcher: "荧惑研究员"
  sl_researcher_desc: "荧惑研究员承载着火星时代的科学之火，在物理学和工程学前沿不断突破。"
  sl_quartermaster: "军需官"
  sl_quartermaster_desc: "军需官管理鸾羽舰队的后勤补给，生产合金并扩展海军容量。"

  # 决议
  decision_sl_rebuild_earth: "重建地球"
  decision_sl_rebuild_earth_desc: "归巢计划的终极回报——将星尘化的地球重新改造为宜居世界。"
  decision_sl_rebuild_earth_requirement: "需要染山霞血脉飞升天赋。"
  decision_sl_homing_broadcast: "归巢广播"
  decision_sl_homing_broadcast_desc: "向银河广播归巢文明的故事，吸引认同我们愿景的同路人。"

  # 法令
  edict_sl_homing_recall: "归巢令"
  edict_sl_homing_recall_desc: "以国家法令号召全民铭记归巢之路，强化文明认同，延长领袖寿命。"

  # 陆军
  sl_homing_volunteer: "归巢志愿军"
  sl_homing_volunteer_desc: "怀揣归巢信念的公民自愿参军。他们不靠重装科技，而靠信念与士气作战。"
  sl_homing_volunteer_plural: "归巢志愿军"

  # 事件链
  sl_chain_earth_legacy: "地球遗产"
  sl_chain_earth_legacy_desc: "揭开星尘化地球的秘密，重拾太阳系的遗产。"
  sl_chain_faction_story: "派系编年史"
  sl_chain_faction_story_desc: "你所选择的派系故事随文明的成熟而展开。"
  sl_chain_convergence: "汇聚"
  sl_chain_convergence_desc: "汉和文明的三脉血脉汇聚，走向共同的命运。"

  # 游戏概念
  concept_sl_origin_homing: "归巢计划"
  concept_sl_origin_homing_desc: "归巢计划是汉和文明的起源故事——太阳系灾变的幸存者在火星上重建家园，如今寻求回归群星中的祖居。三大派系引领旅程：辉夙（遗产）、荧惑（科学）和鸾羽（军事）。"
  concept_sl_huisu: "辉夙派"
  concept_sl_huisu_desc: "博物传承派。辉夙档案官保存汉和文明的集体记忆，从过去汲取力量引导未来。其传统树聚焦凝聚力、考古学和行政效率。"
  concept_sl_martis: "荧惑派"
  concept_sl_martis_desc: "荧惑科学派。荧惑研究员承载火星时代的实验之火，不断突破物理和工程的边界。其传统树聚焦科研速度、异常分析和科学探索。"
  concept_sl_phoenix: "鸾羽派"
  concept_sl_phoenix_desc: "鸾羽军事派。鸾羽指挥官传承最初撤离舰队的铁律。其传统树聚焦舰队扩张、武器系统和地面作战优势。"
  concept_sl_ranshanxia: "染山霞血脉"
  concept_sl_ranshanxia_desc: "染山霞血脉是汉和三大派系传统的汇聚。以曾温暖旧地球苍穹的大气现象命名，代表遗产、科学和力量的统一。"
  concept_sl_homing_tradition: "归巢传统"
  concept_sl_homing_tradition_desc: "每个汉和派系拥有自己的传统树，由对应国策解锁。完成传统树将解锁对应的派系飞升天赋和一座帝国唯一建筑。"

  # 开屏消息
  origins_sl_homing_start_screen: "§H归巢计划§!\n\n千年之前，太阳系被恒星之火吞噬。幸存者——工程师、学者、军人——逃往火星重建家园。他们称之为归巢计划：终有一日，我们将找到回去的路。\n\n如今，三大派系引领着汉和文明：§Y辉夙§!，记忆的守护者；§Y荧惑§!，科学之火的承载者；§Y鸾羽§!，铁律舰队的卫士。\n\n群星在等待。归巢之旅，启程。"

  # 消息类型
  MESSAGE_SL_EVENT_PROGRESS: "归巢进展"
  MESSAGE_SL_EVENT_PROGRESS.s: "归巢事件进展"
  MESSAGE_SL_HOMING_MILESTONE: "归巢里程碑"
  MESSAGE_SL_HOMING_MILESTONE.s: "归巢里程碑"

  # Tier 2 修正器
  modifier_sl_rebuilt_earth: "重建的地球"
  modifier_sl_rebuilt_earth_desc: "人类的祖居之星已经修复。重生的地表充满生命与工业。"
  sl_broadcast_active: "归巢广播中"
  sl_broadcast_active_desc: "我们的故事回荡在银河中，吸引志同道合者前来殖民地。"

  # 事件 17
  event_homing.17.title: "新的黎明"
  event_homing.17.desc: "完成了。星尘散尽，海洋重现，天空再次湛蓝。地球——不是它曾经的样子，而是它可以成为的样子——在群星下重生。千年流亡的终点不是归乡，而是创造。我们没有回到旧世界。我们建造了一个新的。"
  event_homing.17.opt.new_dawn: "欢迎回家。"
```

- [ ] **Step 3: Verify key counts**

Run: `grep -c "building_sl_\|sl_archivist\|sl_researcher\|sl_quartermaster\|decision_sl_\|edict_sl_\|sl_homing_volunteer\|sl_chain_\|concept_sl_\|origins_sl_\|MESSAGE_SL_\|modifier_sl_\|sl_broadcast_\|event_homing.17" Starlike/localisation/l_english/sl_l_english.yml`
Expected: ~52 matching lines (new keys)

Run: Same grep on `l_simp_chinese` file.
Expected: Same count.

- [ ] **Step 4: Commit**

```bash
git add Starlike/localisation/l_english/sl_l_english.yml Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml
git commit -m "feat: add bilingual localization for all Tier 2 systems (~52 key pairs EN+CN)

- 3 buildings, 3 jobs, 2 decisions, 1 edict, 1 army
- 3 event chains, 6 game concepts, 1 start screen
- 2 message types, 2 modifiers, event 17"
```

---

## Task 11: Final Verification

- [ ] **Step 1: Verify all new files exist**

Run:
```bash
ls -la Starlike/common/scripted_triggers/sl_triggers.txt \
      Starlike/common/scripted_effects/sl_effects.txt \
      Starlike/common/buildings/sl_buildings.txt \
      Starlike/common/pop_jobs/sl_jobs.txt \
      Starlike/common/decisions/sl_decisions.txt \
      Starlike/common/edicts/sl_edicts.txt \
      Starlike/common/armies/sl_armies.txt \
      Starlike/common/event_chains/sl_event_chains.txt \
      Starlike/common/message_types/sl_messages.txt \
      Starlike/common/game_concepts/sl_concepts.txt \
      Starlike/common/start_screen_messages/sl_start_screen.txt
```
Expected: All 11 files exist

- [ ] **Step 2: Verify event_homing.17 appended**

Run: `grep "id = event_homing.17" Starlike/events/sl_homing_events.txt`
Expected: Match found

- [ ] **Step 3: Verify chain calls wired**

Run: `grep -c "begin_event_chain\|sl_progress_" Starlike/events/sl_homing_events.txt`
Expected: 16+ matches (3 begin_event_chain + 13+ progress calls)

- [ ] **Step 4: Verify modifier file has 9 blocks**

Run: `grep -c "^[a-z_].*= {" Starlike/common/static_modifiers/sl_modifiers.txt`
Expected: `9`

- [ ] **Step 5: Commit verification tag**

```bash
git commit --allow-empty -m "chore: v0.4 Tier 2 systems verification complete

- 11 new files created ✓
- event_homing.17 appended ✓
- Event chain tracking wired into events 1-16 ✓
- 9 modifiers total ✓
- Bilingual localization complete ✓"
```

---

## Summary Checklist

| Task | Content | Files |
|------|---------|-------|
| 1 | Scripted triggers | 1 new |
| 2 | Scripted effects | 1 new |
| 3 | 3 faction buildings | 1 new |
| 4 | 3 specialist jobs | 1 new |
| 5 | 2 decisions + 1 edict + 2 modifiers | 2 new + 1 edit |
| 6 | 1 army type | 1 new |
| 7 | 3 event chains + 2 msg types | 2 new |
| 8 | 6 game concepts + 1 start screen | 2 new |
| 9 | event_homing.17 + chain wiring | 1 edit |
| 10 | Bilingual localization (~52 pairs) | 2 edit |
| 11 | Final verification | 0 |

**Total:** 11 new files, 4 edited files, 11 tasks.
