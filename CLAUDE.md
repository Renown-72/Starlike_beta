# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Starlike: Project Homing is a **Stellaris v4.3 game mod** built entirely with Paradox Scripting Language (`.txt` files). There is no build system, no test pipeline, and no compiled code. The mod is loaded directly by the Stellaris engine.

**Steam Workshop ID**: 3480510700

**Core gameplay content**:
- Custom origin `origin_homing` with a Sol-like starting system (Mars as homeworld, Earth turned to stellar dust)
- Three faction civics (`civic_huisu`, `civic_martis`, `civic_phoenix_plume`) each gating a councilor
- 4-event narrative chain (`event_homing.1`-`.4`) covering Europa auto-colonization and Earth exploration
- 25-track original soundtrack (Arnaud Roy Vol.1, H-Pi Vol.2)

## Architecture: How the Pieces Connect

The mod's core design is a **dependency chain** that flows from origin through civics to councilors, with events triggered by game hooks and planet flags. Understanding this chain is essential before modifying any file.

```
Starlike.mod (mod descriptor, points engine to Starlike/ directory)
    |
    v
on_game_start_country (common/on_actions/1.txt)
    |
    v  triggers
event_homing.1 (events/Starlike_homing_events.txt)
    |
    v  auto-colonizes Europa via Homing_Europa flag
    |
    v  (later, player surveys Homing_Earth_flag planet)
event_homing.2 --> event_homing.3 --> event_homing.4
    |                   |                   |
    v                   v                   v
    flag-gated      homing_earth_memories  homing_legacy modifier
    planet_event    OR decode path          + stellarite_touched scientist

origin_homing (civics/Starlike_origin.txt)
    |
    +-- initializers = { homing_earth }  --> solar_system_initializers/Homing_initializers.txt
    |
    +-- possible gate for all civics and councilors:
        civic_huisu  --> councilor_huisu  (official, archival tradition)
        civic_martis --> councilor_martis (scientist, Mars reconstruction)
        civic_phoenix_plume --> councilor_phoenix_plume (commander, military)
```

**Critical invariant**: Every civic and councilor is gated by `origin = { value = origin_homing }`. Adding new civics or councilors must preserve this gate or the content will appear outside the intended origin.

## Version Discrepancy

Two `.mod` files exist with different `version` values:
- `Starlike.mod` (repo root): `version="4.3"` -- this is the file used by the Paradox Launcher
- `Starlike/descriptor.mod` (inside mod folder): `version="4.0"` -- this is the file uploaded to Steam Workshop

These should be kept in sync. `supported_version="v4.3.*"` is consistent in both.

## Verifying Changes

There is no automated testing. All verification is manual.

**Launch with mod enabled** via Stellaris launcher. For quick iteration:

```
# Trigger specific events from in-game console (tilde key):
event event_homing.1          # Auto-colonize Europa
event event_homing.2          # Stellar Dust Homeworld (needs planet scope)
event event_homing.3          # Echoes of Home
event event_homing.4          # Legacy of the StarWhispers

# Useful console commands for mod debugging:
observe                       # Switch to observer mode
research_all_technologies     # Unlock all tech (test prerequisites)
```

**Error log location**:
```
%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log
```

Common errors to watch for: missing localization keys (shown as raw key names in-game), undefined modifier names (silent failures), and mismatched flag names between initializer and event files.

## File Loading and Naming Rules

Stellaris loads all `.txt` files within each `common/` subdirectory in **lexical order**. This mod uses two naming strategies:
- `Starlike_*.txt` -- loads after vanilla files starting with lowercase letters
- `00_*.txt` or `1.txt` -- explicit numeric prefix for load-order control

All custom identifiers use these prefixes to avoid collisions with vanilla or other mods:
- `origin_homing`, `homing_*` -- origin and system-level
- `civic_huisu`, `civic_martis`, `civic_phoenix_plume` -- faction civics
- `councilor_huisu`, `councilor_martis`, `councilor_phoenix_plume` -- faction councilors
- `event_homing.*` -- event namespace
- `trait_scientist_stellarite_touched` -- leader trait

## Flag System (Cross-File Communication)

The mod uses Stellaris flags as the primary mechanism for cross-file state sharing. These must remain consistent across files:

| Flag | Set In | Read In | Purpose |
|------|--------|---------|---------|
| `Homing_Sol` | `Homing_initializers.txt` (system flag) | `Starlike_homing_events.txt` (event_homing.1) | Identifies the starting system |
| `Homing_Earth_flag` | `Homing_initializers.txt` (planet flag) | `Starlike_homing_events.txt` (event_homing.2 trigger) | Marks the stellar-dust Earth |
| `Homing_Europa` | `Homing_initializers.txt` (planet flag) | `Starlike_homing_events.txt` (event_homing.1) | Marks Europa for auto-colonization |
| `homing_earth_investigating` | `Starlike_homing_events.txt` (event_homing.2, timed 3650 days) | `Starlike_homing_events.txt` (event_homing.3 trigger) | Investigation in progress |
| `homing_earth_analyzed` | `Starlike_homing_events.txt` (event_homing.3) | `Starlike_homing_events.txt` (event_homing.4 trigger) | Analysis complete gate |
| `homing_earth_legacy_unlocked` | `Starlike_homing_events.txt` (event_homing.4) | `Starlike_homing_events.txt` (event_homing.4 trigger) | Prevents re-triggering |

Renaming or removing any flag without updating all references will silently break the event chain.

## Localization Workflow

Both language files must be updated in lockstep. Every scripted `description`, event `title`/`desc`, and option `name` references a localization key.

Files:
- `Starlike/localisation/l_english/Starlike_l_english.yml`
- `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

Format:
```yaml
l_english:
  key: "Value with §Hcolor codes§! and \nnewlines"
```

Stellaris color codes: `§g` (green), `§H` (yellow highlight), `§R` (red), `§!` (reset). Modifier icons: `£mod_name£`.

## Known Bugs in Source Code

### Duplicate modifier keys (will cause silent override)

In `Starlike_civics.txt`:
- `civic_huisu` defines `country_unity_produces_mult` twice (0.10 and 0.15) -- the second value silently overwrites the first
- `civic_martis` defines `science_ship_survey_speed` twice (0.2 and 0.15) -- same issue

### Missing anomaly trigger mechanism

`event_homing.2` is a `planet_event` with `is_triggered_only = yes` and a `fromfrom` trigger checking `Homing_Earth_flag`. However, no anomaly definition exists in `common/anomalies/` to actually trigger this event when surveying the planet. The `anomaly_exoplanet_01_ambient_object` created in the initializer is a visual ambient object, not a functional anomaly trigger. This means `event_homing.2` (and by extension `.3` and `.4`) cannot fire during normal gameplay.

### Music file paths commented out

All 25 `file =` lines in `music/StarlikeOST.txt` are commented out. The `.ogg` files exist in the repository but the engine cannot load them. Each `song = { name = "..." }` block currently has no associated audio file path.

## Common Development Tasks

### Adding a new civic

1. Add definition block to `common/governments/civics/Starlike_civics.txt` with `possible = { origin = { value = origin_homing } }`
2. Add localization keys: `civic_xxx`, `civic_xxx_desc`, `civic_xxx_effects`, `civic_xxx_negative_effects` in both YAML files
3. If the civic should have a councilor, add the councilor in `common/governments/councilors/Starlike_councilors.txt` with `civic = civic_xxx`

### Extending the event chain

1. All events use `namespace = event_homing` (declared at top of `Starlike_homing_events.txt`)
2. Next available ID: `event_homing.5`
3. Add localization for `event_homing.N.title`, `event_homing.N.desc`, and each `event_homing.N.opt.*`
4. Chain events via `trigger_event = { id = event_homing.N days = X }` in the preceding event's option block

### Adding a new modifier

1. Define in `common/static_modifiers/Starlike_modifiers.txt` with an `icon` reference
2. Apply via `add_modifier = { modifier = "modifier_id" days = -1 }` in events (-1 = permanent)
3. Add `modifier_id` and `modifier_id_desc` localization keys

## Module Index

| Module | Path | Entry File(s) | Content |
|--------|------|---------------|---------|
| Origin + Civics | `common/governments/civics/` | `Starlike_origin.txt`, `Starlike_civics.txt` | origin_homing + 3 faction civics |
| Councilors | `common/governments/councilors/` | `Starlike_councilors.txt` | 3 faction councilors (huisu, martis, phoenix_plume) |
| Solar System | `common/solar_system_initializers/` | `Homing_initializers.txt` | homing_earth system (Sol-like, 10 planets + 5 moons) |
| Modifiers | `common/static_modifiers/` | `Starlike_modifiers.txt` | homing_earth_memories, homing_legacy |
| Traits | `common/traits/` | `00_starlike_leader_traits.txt` | trait_scientist_stellarite_touched |
| On Actions | `common/on_actions/` | `1.txt` | on_game_start_country hook |
| Events | `events/` | `Starlike_homing_events.txt` | 4-event chain (namespace: event_homing) |
| Localization | `localisation/` | `Starlike_l_english.yml`, `Starlike_l_simp_chinese.yml` | Full EN+CN |
| Music | `music/` | `StarlikeOST.txt` + 25 `.ogg` files | Soundtrack metadata (file paths commented) |
| Interface | `interface/` | `Originpictures.gfx` | GFX_origin_project_homing sprite definition |
| Graphics | `gfx/` | (binary only) | 10 loading screens, 2 origin assets (DDS) |

## Reference Documentation

The `代码参考（必看）/` directory contains tutorial examples for various Paradox modding topics. Key files at the root level:
- `P语言教程.txt` -- P-language syntax tutorial
- `群星数值注释.txt` -- Game balance values reference
- `群星common文件夹下的文件的作用.txt` -- Guide to `common/` subdirectory purposes
- `群星里的部分条件.txt` -- Conditions/triggers reference

Tutorial subdirectories with complete example mods: `起源编写/` (origins), `国策和配套的领袖编写/` (civics + councilors), `传统和议程的编写/` (traditions + agendas), `政体的编写/` (authorities), `科技的编写/` (technologies), `自建物种代码/` (custom species), `桌面图，加载图和音乐集/` (UI + music), among others.

## Known Gaps (Not Yet Implemented)

**High priority** -- No anomaly definition file (`common/anomalies/`) exists to trigger `event_homing.2`, breaking the event chain after the opening event.

**Medium priority**:
- No species/portrait definitions (`common/species/`, `gfx/portraits/`)
- No council agendas (`common/council_agendas/`)

**Low priority**:
- Music `file =` paths need uncommenting in `StarlikeOST.txt`
- No ship designs, name lists, or ascension perks
- `descriptor.mod` version mismatch with `Starlike.mod`

---

## Changelog

### 2026-03-31 -- Documentation Audit & Bug Discovery

- Corrected councilor count from 4 to 3 (no `councilor_ruler_copan` exists in source)
- Documented duplicate modifier bug in `civic_huisu` and `civic_martis`
- Identified missing anomaly trigger mechanism for `event_homing.2`
- Added flag cross-reference table and architecture dependency chain
- Added `descriptor.mod` version discrepancy note
- Restructured for faster onboarding with focus on cross-file dependencies

### 2026-03-26 (v0.2.1) -- Documentation & Scan

- Full module documentation scan completed
- All modules documented with CLAUDE.md files

### 2026-03-26 (v0.2.0) -- Faction Civic & Councilor Rewrite

- origin_homing: Removed auth_copan binding; now independent origin compatible with vanilla auth_dictatorial
- New civics: civic_huisu, civic_martis, civic_phoenix_plume
- New councilors: councilor_huisu, councilor_martis, councilor_phoenix_plume
- New events: event_homing.1-.4
- New trait: trait_scientist_stellarite_touched
- New modifiers: homing_earth_memories, homing_legacy
