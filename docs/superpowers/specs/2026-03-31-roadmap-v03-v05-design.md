# Starlike Roadmap v0.3 ~ v0.5 Design Spec

Architecture: **Faction-Driven** -- each version deepens the three-faction identity of the Homing origin.

---

## v0.3: Bug Fixes + Council Agendas

### Bug Fixes

#### 1. Missing anomaly trigger for event_homing.2 (HIGH)

**Problem**: `event_homing.2` is a `planet_event` with `is_triggered_only = yes`, but no anomaly definition exists to fire it when surveying the stellar-dust Earth. This breaks the entire exploration event chain (events 2-4).

**Fix**: Create `Starlike/common/anomalies/Starlike_anomalies.txt` with an anomaly definition that:
- Spawns on planets with `Homing_Earth_flag`
- On completion, fires `event_homing.2` via `on_success` block
- Uses appropriate category (e.g., `anomaly_category = anomaly_PLANET`)

Additionally, remove the redundant `create_ambient_object` from `event_homing.1` (line 32-34) since the initializer already creates it in `Homing_initializers.txt` (line 29-32). The ambient object in the initializer is the correct placement; the one in the event is a duplicate.

**Files affected**:
- NEW: `Starlike/common/anomalies/Starlike_anomalies.txt`
- EDIT: `Starlike/events/Starlike_homing_events.txt` (remove duplicate ambient object)
- ADD: Localization keys for anomaly title/desc in both YAML files

#### 2. Duplicate modifier keys in civics (MEDIUM)

**Problem**: Paradox engine silently uses the last value when a key appears twice in the same block.

In `Starlike/common/governments/civics/Starlike_civics.txt`:
- `civic_huisu` line 16 `country_unity_produces_mult = 0.10` and line 18 `country_unity_produces_mult = 0.15` -- second overwrites first
- `civic_martis` line 39 `science_ship_survey_speed = 0.2` and line 41 `science_ship_survey_speed = 0.15` -- second overwrites first

**Fix**:
- `civic_huisu`: Remove the 0.10 line, keep 0.15. The thematic intent is one combined unity bonus from archival tradition.
- `civic_martis`: Keep 0.20 (the higher value aligns with "Mars reconstruction science"). Remove the 0.15 line, or replace it with a different modifier like `ship_anomaly_research_speed_mult = 0.15`.

**Files affected**: `Starlike/common/governments/civics/Starlike_civics.txt`

#### 3. Music file paths commented out (LOW)

**Problem**: All 25 `file =` lines in `music/StarlikeOST.txt` are commented out. The `.ogg` files exist but the engine cannot load them.

**Fix**: Uncomment all `file =` lines. Verify each path matches actual `.ogg` file locations.

**Files affected**: `Starlike/music/StarlikeOST.txt`

#### 4. Version discrepancy between mod descriptors (LOW)

**Problem**: `Starlike.mod` has `version="4.3"` but `Starlike/descriptor.mod` has `version="4.0"`.

**Fix**: Update `Starlike/descriptor.mod` to `version="4.3"`.

**Files affected**: `Starlike/descriptor.mod`

### New Feature: Council Agendas

Create `Starlike/common/council_agendas/Starlike_council_agendas.txt` with three faction-specific agendas.

#### agenda_huisu (Museum Restoration Agenda)

```
agenda_huisu = {
    potential = {
        owner = { has_civic = civic_huisu }
    }
    modifier = {
        category_archaeostudies_research_speed_mult = 0.15
        country_unity_produces_mult = 0.10
    }
}
```

Theme: Archival and archaeological tradition. Boosts archaeology research speed and unity output.

#### agenda_martis (Scientific Vanguard Agenda)

```
agenda_martis = {
    potential = {
        owner = { has_civic = civic_martis }
    }
    modifier = {
        all_technology_research_speed = 0.05
        science_ship_survey_speed = 0.15
    }
}
```

Theme: Mars-reconstruction-era scientific elite. Boosts all research speed and survey speed.

#### agenda_phoenix_plume (Military Expansion Agenda)

```
agenda_phoenix_plume = {
    potential = {
        owner = { has_civic = civic_phoenix_plume }
    }
    modifier = {
        country_naval_cap_mult = 0.10
        army_damage_mult = 0.10
    }
}
```

Theme: Phoenix Plume militarist tradition. Boosts naval capacity and army damage.

**Files affected**:
- NEW: `Starlike/common/council_agendas/Starlike_council_agendas.txt`
- ADD: Localization keys (`agenda_huisu`, `agenda_huisu_desc`, etc.) in both YAML files

### v0.3 Localization Keys Required

English and Chinese localization must be added for:
- 1 anomaly (title + desc + 2 option strings)
- 3 agendas (name + desc each = 6 keys)

Total: ~10 new localization key pairs.

---

## v0.4: Traditions + Ascension Perks + Extended Events

### Tradition Tree: "Homing" (Shared)

One tradition tree available to all `origin_homing` empires. This represents the shared civilizational identity, while faction differentiation comes from civics/councilors/agendas.

**File**: NEW `Starlike/common/traditions/Starlike_traditions.txt`

| Node | Internal ID | Name (EN) | Name (CN) | Effect |
|------|-------------|-----------|-----------|--------|
| Adopt | `tr_homing_adopt` | Will of Homing | gui chao zhi zhi | +10% unity, +15% anomaly discovery chance |
| Tradition 1 | `tr_homing_stardust_memory` | Stardust Memories | xing chen ji yi | +25% archaeology research speed |
| Tradition 2 | `tr_homing_exile_routes` | Exile Routes | liu wang hang lu | +15% subspace jump speed, +10% survey speed |
| Tradition 3 | `tr_homing_colonial_legacy` | Colonial Legacy | zhi min yi chan | +15% colony development speed |
| Tradition 4 | `tr_homing_tripartite_accord` | Tripartite Accord | san pai gong zhi | +20% faction attraction, -10% empire size penalty |
| Tradition 5 | `tr_homing_stellar_echoes` | Stellar Echoes | xing yu hui xiang | +25% leader XP gain, +10 leader lifespan |
| Finish | `tr_homing_finish` | Homing Revelation | gui chao qi shi | Unlock ascension perk slot + special edict |

**Adoption requirement**: `origin = { value = origin_homing }`

**Files affected**:
- NEW: `Starlike/common/traditions/Starlike_traditions.txt`
- NEW: `Starlike/common/tradition_categories/Starlike_tradition_categories.txt` (category definition)
- ADD: ~16 localization key pairs (7 traditions x name + desc + adoption/finish flavor)

### Ascension Perks (2)

**File**: NEW `Starlike/common/ascension_perks/Starlike_ascension_perks.txt`

#### ap_alpenglow (Alpenglow)

- **Prerequisite**: `tr_homing_finish` (Homing tradition tree completed)
- **Modifier**: `all_technology_research_speed = 0.15`, `ship_anomaly_research_speed_mult = 0.50`
- **On-adopt effect**: Fires `event_homing.14` (the beginning of the late-game convergence chain)
- **Localization**: CN = "shu hui" (Alpenglow)

#### ap_stellar_return (Stellar Return)

- **Prerequisite**: `ap_alpenglow` + mid-game tech (e.g., `tech_gateway_construction` or equivalent)
- **Modifier**: `planet_jobs_produces_mult = 0.10`
- **On-adopt effect**: Unlocks "Rebuild Earth" decision on the `Homing_Earth_flag` planet
- **Localization**: CN = "xing gui" (Stellar Return)

**Files affected**:
- NEW: `Starlike/common/ascension_perks/Starlike_ascension_perks.txt`
- ADD: ~6 localization key pairs

### Extended Event Chains

All events use the existing `namespace = event_homing`.

#### Huisu Faction Mid-Game Chain (events 5-7)

**Trigger**: Civic `civic_huisu` + mid-game year ~50+

| Event | Type | Trigger | Narrative | Outcome |
|-------|------|---------|-----------|---------|
| event_homing.5 | country_event | Auto (year > 50, has civic_huisu) | Museum curator discovers fragments of pre-departure Earth archive | Choose: catalog or exhibit |
| event_homing.6 | country_event | 90 days after .5 (catalog) | Archive reveals pre-flight colonist manifest | +unity, +pop happiness temporary modifier |
| event_homing.7 | country_event | 90 days after .5 (exhibit) | Exhibition galvanizes public | +influence, temporary governing ethics attraction |

#### Martis Faction Mid-Game Chain (events 8-10)

**Trigger**: Civic `civic_martis` + mid-game year ~50+

| Event | Type | Trigger | Narrative | Outcome |
|-------|------|---------|-----------|---------|
| event_homing.8 | country_event | Auto (year > 50, has civic_martis) | Underground lab on Mars yields sealed experiments | Choose: replicate or reverse-engineer |
| event_homing.9 | country_event | 120 days after .8 (replicate) | Experiment succeeds, new material synthesized | +physics research, temporary research modifier |
| event_homing.10 | country_event | 120 days after .8 (reverse-engineer) | Reverse engineering reveals propulsion concept | +engineering research, +ship speed modifier |

#### Phoenix Plume Faction Mid-Game Chain (events 11-13)

**Trigger**: Civic `civic_phoenix_plume` + mid-game year ~50+

| Event | Type | Trigger | Narrative | Outcome |
|-------|------|---------|-----------|---------|
| event_homing.11 | country_event | Auto (year > 50, has civic_phoenix_plume) | Wreckage of original Phoenix Plume warship located | Choose: salvage or memorial |
| event_homing.12 | country_event | 60 days after .11 (salvage) | Weapons technology recovered | +fire rate, temporary army buff |
| event_homing.13 | country_event | 60 days after .11 (memorial) | Memorial inspires military recruitment | +naval cap, +army morale |

#### Late-Game Convergence Chain (events 14-16)

**Trigger**: Has `ap_alpenglow` ascension perk

| Event | Type | Trigger | Narrative | Outcome |
|-------|------|---------|-----------|---------|
| event_homing.14 | country_event | On adopting ap_alpenglow | Faint signal detected from stellar-dust Earth | Mandatory: begin investigation |
| event_homing.15 | country_event | 360 days after .14 | Signal decoded: Earth's core contains compressed stellar data | Choose: harness or preserve |
| event_homing.16 | country_event | 180 days after .15 (harness) OR on adopting ap_stellar_return | Earth Reconstruction complete or data preserved | Final narrative payoff + permanent modifier |

**Files affected**:
- EDIT: `Starlike/events/Starlike_homing_events.txt` (add events 5-16)
- Possibly NEW: `Starlike/common/on_actions/Starlike_on_actions.txt` (for mid-game auto-triggers)
- ADD: ~36 localization key pairs (12 events x title + desc + options)

### v0.4 Localization Keys Required

- Tradition tree: ~16 key pairs
- Ascension perks: ~6 key pairs
- Events 5-16: ~36 key pairs

Total: ~58 new localization key pairs.

---

## v0.5: World-Building + Cross-Faction Interaction

### Prescripted Species

**File**: NEW `Starlike/common/prescripted_countries/Starlike_prescripted_species.txt`

Define the canonical Homing civilization species for the empire picker:
- Species class: Human (reuse vanilla `HUMAN` portrait group -- no custom portrait art needed)
- Species name: Homing Terrans / gui chao ren (CN)
- Traits: `Intelligent`, `Nomadic`, `Wasteful` (exile civilization flavor)
- Homeworld: Mars (pc_city), already defined in initializer

This gives the origin a preset empire option in the species selection screen.

### Name Lists

**File**: NEW `Starlike/common/name_lists/Starlike_name_lists.txt`

Categories to define:
- **Leader names**: Mixed East Asian + Latin + classical sci-fi (e.g., Ling, Ashford, Kazuki, Voss)
- **Ship names**: Named after celestial bodies and narrative elements (e.g., Alpenglow / shu hui, Phoenix Plume / luan yu, Martis / ying huo, Stardust / xing chen)
- **Fleet names**: Expedition-themed (e.g., Homing Fleet, Return Armada)
- **Planet names**: Nostalgic Earth geography + astronomical catalog style
- **Army names**: Faction-themed (Phoenix Guard, Martis Vanguard)

~100-150 name entries across all categories.

### Starting Ship Designs

**File**: NEW `Starlike/common/ship_designs/Starlike_ship_designs.txt`

Reuse vanilla ship hulls with Homing-themed names:
- 1 Science Ship: "Explorer" class
- 1 Construction Ship: "Pioneer" class
- 3 Corvettes: "Sentinel" class (standard loadout)

Starting fleet composition matches vanilla defaults but with custom names pulled from the name list.

### Cross-Faction Interaction Events (events 17-20)

| Event | Type | Trigger | Narrative | Outcome |
|-------|------|---------|-----------|---------|
| event_homing.17 | country_event | Has 2+ Homing civics simultaneously | Faction council summit: debate priorities | Player chooses faction leaning, gains temporary aligned modifier |
| event_homing.18 | country_event | Has ap_stellar_return + completed event 16 | Three factions unite for Earth expedition | Narrative conclusion + permanent "Unified Homing" modifier |
| event_homing.19 | country_event | First contact with alien empire | Homing civilization reflects on exile identity | +influence, special diplomatic option |
| event_homing.20 | country_event | Encounter another Homing empire (unlikely, AI weight = 0, but defensive) | Two Homing fleets meet | Diplomatic bonus, shared research agreement |

**Files affected**:
- EDIT: `Starlike/events/Starlike_homing_events.txt` (add events 17-20)
- NEW: `Starlike/common/prescripted_countries/Starlike_prescripted_species.txt`
- NEW: `Starlike/common/name_lists/Starlike_name_lists.txt`
- NEW: `Starlike/common/ship_designs/Starlike_ship_designs.txt`
- ADD: ~30+ localization key pairs (events + species + names)

### v0.5 Localization Keys Required

- Species/prescripted: ~4 key pairs
- Name lists: embedded (no external YAML keys)
- Ship designs: ~5 key pairs
- Events 17-20: ~12 key pairs

Total: ~21 new localization key pairs.

---

## Summary: Full Roadmap

| Version | Theme | New Files | New Loc Keys | Content |
|---------|-------|-----------|-------------|---------|
| v0.3 | Bug Fix + Agendas | 2 new | ~10 pairs | 4 bug fixes, 3 council agendas |
| v0.4 | Depth | 4 new | ~58 pairs | 1 tradition tree (7 nodes), 2 ascension perks, 12 events |
| v0.5 | World-Building | 3 new | ~21 pairs | Species preset, name list, ship designs, 4 cross-faction events |

**Total new files**: 9
**Total new localization key pairs**: ~89
**Total new events**: 16 (events 5-20)

### Dependency Chain

```
v0.3 Bug Fixes (must be first -- unblocks event chain)
  |
  v
v0.3 Council Agendas (independent of bug fixes, can parallel)
  |
  v
v0.4 Tradition Tree (independent)
  |
  +-- v0.4 Ascension Perks (depends on tradition tree completion)
  |     |
  |     v
  |   v0.4 Late-game events 14-16 (depends on ap_alpenglow)
  |
  +-- v0.4 Faction events 5-13 (independent of traditions, can parallel)
  |
  v
v0.5 Species + Names + Ships (independent of v0.4 events)
  |
  v
v0.5 Cross-faction events 17-20 (depends on v0.4 late-game chain for event 18)
```
