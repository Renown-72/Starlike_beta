# Repository Guidelines

## Project Structure & Module Organization

This repository contains a Stellaris 4.3 total-content mod, not a conventional application. `Starlike/` is the loadable mod directory. Gameplay definitions live under `Starlike/common/`, with one folder per Paradox definition type (for example, `governments/`, `on_actions/`, `situations/`, and `traditions/`). Narrative scripts are in `Starlike/events/`; English and Simplified Chinese text is in `Starlike/localisation/`. Art, portraits, interface assets, and music are stored under `Starlike/gfx/`, `Starlike/interface/`, and `Starlike/music/`. Keep `Starlike/descriptor.mod` aligned with the root `Starlike.mod` when changing version metadata.

## Build, Test, and Development Commands

There is no compilation step or automated test suite. Install or link the mod into the Stellaris user mod directory, enable it in the launcher, and start a new game for structural changes.

- `git diff --check` — detects whitespace errors before review.
- `rg -n "event_homing|sl_bloodline" Starlike` — traces event and bloodline references.
- `Get-Content "$env:USERPROFILE\Documents\Paradox Interactive\Stellaris\logs\error.log" -Tail 100` — reviews recent parser and runtime errors after an in-game test.

Use Stellaris `-debug_mode` when validating events, localization, portraits, or scripted effects.

## Coding Style & Naming Conventions

Use four-space indentation in Paradox script files and follow neighboring block layout. Prefer lowercase `snake_case` identifiers with the existing `sl_` or `sl_copan_` prefix. Event IDs use `namespace.number`, such as `event_homing.14`. Keep matching localization keys in both language trees; filenames must end in `_l_english.yml` or `_l_simp_chinese.yml`. Preserve localization encoding and headers (`l_english:` / `l_simp_chinese:`), and escape quotes inside localized strings.

## Testing Guidelines

Test with a new game using the `origin_homing` origin. Check the start setup, Earth anomaly, selected civic route, faction events, ascension unlocks, and Earth reconstruction. Inspect `error.log` after every run. Changes to portraits or interface assets should include screenshots; changes to event chains should document console-assisted reproduction steps.

## Commit & Pull Request Guidelines

Follow the existing Conventional Commit style: `feat:`, `fix:`, `docs:`, `refactor:`, or `chore:` followed by a concise Chinese or English summary. Keep each commit focused. Pull requests should state the affected systems, Stellaris version, whether a new save is required, verification steps, relevant log excerpts, and screenshots for visible changes. Link related issues or Workshop reports when available.

## Configuration Tips

Do not commit developer-specific launcher paths, save files, or generated documentation. Avoid changing `remote_file_id` unless intentionally transferring Workshop ownership.
