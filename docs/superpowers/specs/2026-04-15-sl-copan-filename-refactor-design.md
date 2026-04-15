# 文件重命名规范：sl_ → sl_copan_

## 背景与目标

当前模组文件使用 `sl_` 前缀，不利于 2.0 版本后多阵营扩展。本次重构将所有 `sl_` 文件统一重命名为 `sl_copan_`，明确标识"一体汉和"核心阵营相关代码，为后续新阵营腾出命名空间。

## 参考规范

女仆模组命名结构：
- 核心文件：`000_ms_origins.txt`（三位序号 + 前缀）
- 普通文件：`ms_civics.txt`（前缀 + 功能名）

## 重命名规则

### 1. 代码文件（`common/`、`events/` 等）

| 当前路径 | 新路径 | 说明 |
|---|---|---|
| `common/governments/civics/000_sl_origin.txt` | `common/governments/civics/000_sl_copan_origin.txt` | 保留 000 序号 |
| `common/governments/civics/sl_civics.txt` | `common/governments/civics/sl_copan_civics.txt` | — |
| `common/governments/councilors/sl_councilors.txt` | `common/governments/councilors/sl_copan_councilors.txt` | — |
| `common/council_agendas/sl_council_agendas.txt` | `common/council_agendas/sl_copan_council_agendas.txt` | — |
| `common/static_modifiers/sl_modifiers.txt` | `common/static_modifiers/sl_copan_modifiers.txt` | — |
| `common/traditions/sl_traditions_huisu.txt` | `common/traditions/sl_copan_traditions_huisu.txt` | — |
| `common/traditions/sl_traditions_martis.txt` | `common/traditions/sl_copan_traditions_martis.txt` | — |
| `common/traditions/sl_traditions_phoenix.txt` | `common/traditions/sl_copan_traditions_phoenix.txt` | — |
| `common/traditions/sl_tradition_categories.txt` | `common/traditions/sl_copan_tradition_categories.txt` | — |
| `common/ascension_perks/00_sl_ascension.txt` | `common/ascension_perks/00_sl_copan_ascension.txt` | 保留 00 序号 |
| `common/anomalies/sl_anomalies.txt` | `common/anomalies/sl_copan_anomalies.txt` | — |
| `common/decisions/sl_decisions.txt` | `common/decisions/sl_copan_decisions.txt` | — |
| `common/edicts/sl_edicts.txt` | `common/edicts/sl_copan_edicts.txt` | — |
| `common/armies/sl_armies.txt` | `common/armies/sl_copan_armies.txt` | — |
| `common/event_chains/sl_event_chains.txt` | `common/event_chains/sl_copan_event_chains.txt` | — |
| `common/message_types/sl_messages.txt` | `common/message_types/sl_copan_messages.txt` | — |
| `common/start_screen_messages/sl_start_screen.txt` | `common/start_screen_messages/sl_copan_start_screen.txt` | — |
| `common/game_concepts/sl_concepts.txt` | `common/game_concepts/sl_copan_concepts.txt` | — |
| `common/strategic_resources/sl_strategic_resources.txt` | `common/strategic_resources/sl_copan_strategic_resources.txt` | — |
| `common/technology/sl_technologies.txt` | `common/technology/sl_copan_technologies.txt` | — |
| `common/buildings/sl_buildings.txt` | `common/buildings/sl_copan_buildings.txt` | — |
| `common/pop_jobs/sl_jobs.txt` | `common/pop_jobs/sl_copan_jobs.txt` | — |
| `common/scripted_triggers/sl_triggers.txt` | `common/scripted_triggers/sl_copan_triggers.txt` | — |
| `common/scripted_effects/sl_effects.txt` | `common/scripted_effects/sl_copan_effects.txt` | — |
| `common/situations/sl_situations.txt` | `common/situations/sl_copan_situations.txt` | — |
| `common/policies/sl_policies.txt` | `common/policies/sl_copan_policies.txt` | — |
| `common/pop_faction_types/sl_factions.txt` | `common/pop_faction_types/sl_copan_factions.txt` | — |
| `events/sl_homing_events.txt` | `events/sl_copan_homing_events.txt` | — |
| `events/sl_bloodline_events.txt` | `events/sl_copan_bloodline_events.txt` | — |

### 2. 本地化文件（`localisation/`）

| 当前路径 | 新路径 |
|---|---|
| `localisation/l_english/sl_l_english.yml` | `localisation/l_english/sl_copan_l_english.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese.yml` |
| `localisation/l_english/sl_l_english_events.yml` | `localisation/l_english/sl_copan_l_english_events.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_events.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_events.yml` |
| `localisation/l_english/sl_l_english_governments.yml` | `localisation/l_english/sl_copan_l_english_governments.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_governments.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_governments.yml` |
| `localisation/l_english/sl_l_english_traits.yml` | `localisation/l_english/sl_copan_l_english_traits.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_traits.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_traits.yml` |
| `localisation/l_english/sl_l_english_messages.yml` | `localisation/l_english/sl_copan_l_english_messages.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_messages.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_messages.yml` |
| `localisation/l_english/sl_l_english_economy.yml` | `localisation/l_english/sl_copan_l_english_economy.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_economy.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_economy.yml` |
| `localisation/l_english/sl_l_english_factions.yml` | `localisation/l_english/sl_copan_l_english_factions.yml` |
| `localisation/l_simp_chinese/sl_l_simp_chinese_factions.yml` | `localisation/l_simp_chinese/sl_copan_l_simp_chinese_factions.yml` |
| `localisation/l_english/sl_name_lists_copan_l_english.yml` | `localisation/l_english/sl_copan_name_lists_l_english.yml` |
| `localisation/l_simp_chinese/sl_name_lists_copan_l_simp_chinese.yml` | `localisation/l_simp_chinese/sl_copan_name_lists_l_simp_chinese.yml` |

### 3. 保留原样的文件

以下文件不在本次重命名范围：
- `Starlike.mod`（模组描述符）
- `Starlike/descriptor.mod`
- `Starlike/music/`（音乐资源，用户确认不修改）
- `Starlike/gfx/`（图形资源，按原样）
- `Starlike/common/solar_system_initializers/Homing_initializers.txt`（系统初始化，与阵营无关）

## 实施约束

1. **原子性**：使用 `git mv` 保持 git 历史
2. ** Stellaris 加载顺序**：重命名后需确保字母序不变（已保留 000/00 序号）
3. **引用更新**：所有代码内的文件路径引用需同步更新

## 验证清单

- [ ] 所有 `sl_` 文件已重命名为 `sl_copan_`
- [ ] 无遗漏的 `sl_` 前缀文件（除保留列表外）
- [ ] `git mv` 历史完整
- [ ] Stellaris 可正常加载模组
