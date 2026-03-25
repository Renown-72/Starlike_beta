# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Starlike: Project Homing (偌星：归巢计划) is a **Stellaris v4.0 game mod** that adds:
- Original soundtrack (25 tracks by Arnaud Roy and H-Pi)
- Custom origin: "Project Homing" (origin_homing) — locked to the "homing_earth" solar system
- Custom authority: "Commonwealth of Pan-Galaxy Unified" (auth_copan)
- Custom civic: "Museum Heritage" (civic_huisu_museum)
- Custom councilor: Ruler of COPAN (councilor_ruler_copan)
- Custom event chain via `event_homing.*` namespace

The mod does not use traditional programming languages — it is built entirely with **Paradox Scripting Language** (declarative `.txt` files) loaded by the Stellaris engine.

## Tech Stack

- **Game engine**: Paradox Interactive Stellaris v4.0
- **Modding language**: Paradox Scripting Language (P-language) — `.txt` files
- **Localization**: YAML (`.yml`) — one file per language under `localisation/`
- **Graphics**: DDS textures, `.gfx` definition files
- **Audio**: OGG Vorbis music tracks
- **IDE**: JetBrains IntelliJ IDEA (configured in `.idea/`)
- **Version control**: Git

There is **no build/test pipeline**. The mod is loaded directly by the Stellaris engine. To verify changes, launch the game with the mod enabled.

## Directory Structure

```
Starlike/                          # Main mod directory (what the .mod file points to)
├── common/                        # Game data definitions
│   ├── governments/
│   │   ├── authorities/           # Government authority types (auth_copan)
│   │   ├── civics/               # Civic policies (Starlike_origin.txt, Starlike_civics.txt)
│   │   └── councilors/           # Cabinet positions (councilor_ruler_copan)
│   ├── solar_system_initializers/ # Galaxy generation templates (homing_earth)
│   └── on_actions/               # Game event hooks (on_game_start_country)
├── events/                        # Event definitions (event_homing.* namespace)
├── localisation/
│   ├── l_english/                # English strings
│   └── l_simp_chinese/           # Simplified Chinese strings
├── music/                         # OGG soundtrack files + StarlikeOST.txt
├── interface/                     # UI/GUI gfx definitions
└── gfx/                          # Texture assets (DDS format)
代码参考（必看）/                  # 60+ reference tutorials — read before adding new features
```

## Paradox Scripting Patterns

### File naming
- Files are numbered/prefixed (e.g., `1.txt`, `Starlike_origin.txt`) — Stellaris loads in lexical order within a directory. Use `Starlike_*.txt` to ensure mod content loads after vanilla equivalents.
- All new definition types must be prefixed with the mod namespace to avoid collision (e.g., `auth_copan`, `origin_homing`, `event_homing.*`).

### Common block types
```pdx
# Authority (government type)
auth_copan = {
    election_term_years = 10
    color = { 81 140 44 255 }
    ruler_council_position = councilor_ruler_copan
    possible = { ethics = { ... } }
    country_modifier = { ... }
}

# Origin (starting condition)
origin_homing = {
    is_origin = yes
    icon = "gfx/interface/icons/origins/origins_homing.dds"
    initializers = { homing_earth }
    modifier = { ... }
}

# Civic (policy civic)
civic_huisu_museum = {
    description = "civic_huisu_museum_effects"
    possible = { OR = { authority = { value = auth_copan } } }
}

# Councilor
councilor_ruler_copan = {
    leader_class = { official scientist commander }
    modifier = { ... }
}

# Solar system initializer
homing_earth = {
    class = "sc_g"
    usage = origin
    flags = { Homing_Sol }
    planet = { name = "NAME_Sol" class = "pc_g_star" ... }
}

# Event (namespace required at top of file)
namespace = event_homing
country_event = {
    id = event_homing.1
    is_triggered_only = yes
    immediate = { ... }
}
```

### Localization workflow
1. Add the key to both `localisation/l_english/Starlike_l_english.yml` and `localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`.
2. Reference keys in script with quoted string: `description = "civic_huisu_museum_effects"`.
3. Stellaris parses the YAML key after the colon as the value — no quotes needed in YAML values unless they contain special characters.
4. Chinese text supports Stellaris color codes: `§g...§!`, `§H...§!`, `§R...§!`, `£mod_...£`.

### Graphics (.gfx files)
- `interface/Originpictures.gfx` — defines `spriteType` entries mapping GFX constants to texture files.
- Texture paths are relative to the mod root: `gfx/event_pictures/origins/origins_homing.dds`.
- New portrait/icon assets go under `gfx/` with corresponding `.gfx` sprite definitions.

## Common Development Tasks

### Adding a new authority
1. Create `common/governments/authorities/Starlike_*.txt`.
2. Add localization keys for the new authority in both YAML files.
3. Reference in `origin_*` or `civic_*` via `authority = { value = auth_new }`.

### Adding a new event chain
1. Choose a namespace: `namespace = event_homing` (extend existing) or create a new one.
2. Define `country_event`, `planet_event`, or `galactic_community_event` blocks.
3. Add `id = namespace.N` where N is a unique integer.
4. Trigger via `events = { namespace.N }` in on_actions, decisions, or other events.
5. Add localization for `namespace.N.title`, `namespace.N.desc`, and any option strings.

### Adding music tracks
1. Place `.ogg` files under `music/`.
2. Add `song = { name = "Track_Name" }` entries to `music/StarlikeOST.txt`, with the `file =` line commented out if the files are not yet in place.

### Testing changes
Launch Stellaris with the mod enabled. Paradox scripts are hot-reloaded in the launcher (Paradox Mod System). For event chains, use `event_homing.1` or add debug triggers to fire events manually.

## Reference Documentation

The `代码参考（必看）/` directory contains 60+ tutorial folders covering every aspect of Paradox modding. Key references:
- `P语言教程.txt` — P-language syntax tutorial
- `群星数值注释.txt` — Game balance values reference
- `群星common文件夹下的文件的作用.txt` — `common/` directory structure guide
- Topic-specific subfolders: 起源编写, 政体的编写, 国策和配套的领袖编写, 恒星基地相关和模块的制作, 传统和议程的编写, etc.
