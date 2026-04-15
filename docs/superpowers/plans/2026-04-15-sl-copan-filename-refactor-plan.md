# sl_copan_ 文件重命名实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将所有 `sl_` 前缀文件重命名为 `sl_copan_`，使用 `git mv` 保持 git 历史

**Architecture:** 纯文件重命名操作，无代码逻辑变更。使用 `git mv` 保持原子性，确保 Stellaris 字母序加载顺序不变（已保留 000/00 序号）

**Tech Stack:** Git mv, bash

---

## 文件映射总表

### 代码文件（TXT）

| # | 当前路径 | 新路径 |
|---|---|---|
| 1 | `Starlike/common/governments/civics/000_sl_origin.txt` | `Starlike/common/governments/civics/000_sl_copan_origin.txt` |
| 2 | `Starlike/common/governments/civics/sl_civics.txt` | `Starlike/common/governments/civics/sl_copan_civics.txt` |
| 3 | `Starlike/common/governments/councilors/sl_councilors.txt` | `Starlike/common/governments/councilors/sl_copan_councilors.txt` |
| 4 | `Starlike/common/council_agendas/sl_council_agendas.txt` | `Starlike/common/council_agendas/sl_copan_council_agendas.txt` |
| 5 | `Starlike/common/static_modifiers/sl_modifiers.txt` | `Starlike/common/static_modifiers/sl_copan_modifiers.txt` |
| 6 | `Starlike/common/traditions/sl_traditions_huisu.txt` | `Starlike/common/traditions/sl_copan_traditions_huisu.txt` |
| 7 | `Starlike/common/traditions/sl_traditions_martis.txt` | `Starlike/common/traditions/sl_copan_traditions_martis.txt` |
| 8 | `Starlike/common/traditions/sl_traditions_phoenix.txt` | `Starlike/common/traditions/sl_copan_traditions_phoenix.txt` |
| 9 | `Starlike/common/traditions/sl_tradition_categories.txt` | `Starlike/common/traditions/sl_copan_tradition_categories.txt` |
| 10 | `Starlike/common/ascension_perks/00_sl_ascension.txt` | `Starlike/common/ascension_perks/00_sl_copan_ascension.txt` |
| 11 | `Starlike/common/anomalies/sl_anomalies.txt` | `Starlike/common/anomalies/sl_copan_anomalies.txt` |
| 12 | `Starlike/common/decisions/sl_decisions.txt` | `Starlike/common/decisions/sl_copan_decisions.txt` |
| 13 | `Starlike/common/edicts/sl_edicts.txt` | `Starlike/common/edicts/sl_copan_edicts.txt` |
| 14 | `Starlike/common/armies/sl_armies.txt` | `Starlike/common/armies/sl_copan_armies.txt` |
| 15 | `Starlike/common/event_chains/sl_event_chains.txt` | `Starlike/common/event_chains/sl_copan_event_chains.txt` |
| 16 | `Starlike/common/message_types/sl_messages.txt` | `Starlike/common/message_types/sl_copan_messages.txt` |
| 17 | `Starlike/common/start_screen_messages/sl_start_screen.txt` | `Starlike/common/start_screen_messages/sl_copan_start_screen.txt` |
| 18 | `Starlike/common/game_concepts/sl_concepts.txt` | `Starlike/common/game_concepts/sl_copan_concepts.txt` |
| 19 | `Starlike/common/strategic_resources/sl_strategic_resources.txt` | `Starlike/common/strategic_resources/sl_copan_strategic_resources.txt` |
| 20 | `Starlike/common/technology/sl_technologies.txt` | `Starlike/common/technology/sl_copan_technologies.txt` |
| 21 | `Starlike/common/buildings/sl_buildings.txt` | `Starlike/common/buildings/sl_copan_buildings.txt` |
| 22 | `Starlike/common/pop_jobs/sl_jobs.txt` | `Starlike/common/pop_jobs/sl_copan_jobs.txt` |
| 23 | `Starlike/common/scripted_triggers/sl_triggers.txt` | `Starlike/common/scripted_triggers/sl_copan_triggers.txt` |
| 24 | `Starlike/common/scripted_effects/sl_effects.txt` | `Starlike/common/scripted_effects/sl_copan_effects.txt` |
| 25 | `Starlike/common/situations/sl_situations.txt` | `Starlike/common/situations/sl_copan_situations.txt` |
| 26 | `Starlike/common/policies/sl_policies.txt` | `Starlike/common/policies/sl_copan_policies.txt` |
| 27 | `Starlike/common/pop_faction_types/sl_factions.txt` | `Starlike/common/pop_faction_types/sl_copan_factions.txt` |
| 28 | `Starlike/events/sl_homing_events.txt` | `Starlike/events/sl_copan_homing_events.txt` |
| 29 | `Starlike/events/sl_bloodline_events.txt` | `Starlike/events/sl_copan_bloodline_events.txt` |

### 本地化文件（YML）

| # | 当前路径 | 新路径 |
|---|---|---|
| 30 | `Starlike/localisation/l_english/sl_l_english.yml` | `Starlike/localisation/l_english/sl_copan_l_english.yml` |
| 31 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese.yml` |
| 32 | `Starlike/localisation/l_english/sl_l_english_events.yml` | `Starlike/localisation/l_english/sl_copan_l_english_events.yml` |
| 33 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_events.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_events.yml` |
| 34 | `Starlike/localisation/l_english/sl_l_english_governments.yml` | `Starlike/localisation/l_english/sl_copan_l_english_governments.yml` |
| 35 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_governments.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_governments.yml` |
| 36 | `Starlike/localisation/l_english/sl_l_english_traits.yml` | `Starlike/localisation/l_english/sl_copan_l_english_traits.yml` |
| 37 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_traits.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_traits.yml` |
| 38 | `Starlike/localisation/l_english/sl_l_english_messages.yml` | `Starlike/localisation/l_english/sl_copan_l_english_messages.yml` |
| 39 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_messages.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_messages.yml` |
| 40 | `Starlike/localisation/l_english/sl_l_english_economy.yml` | `Starlike/localisation/l_english/sl_copan_l_english_economy.yml` |
| 41 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_economy.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_economy.yml` |
| 42 | `Starlike/localisation/l_english/sl_l_english_factions.yml` | `Starlike/localisation/l_english/sl_copan_l_english_factions.yml` |
| 43 | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_factions.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_factions.yml` |
| 44 | `Starlike/localisation/l_english/sl_name_lists_copan_l_english.yml` | `Starlike/localisation/l_english/sl_copan_name_lists_l_english.yml` |
| 45 | `Starlike/localisation/l_simp_chinese/sl_name_lists_copan_l_simp_chinese.yml` | `Starlike/localisation/l_simp_chinese/sl_copan_name_lists_l_simp_chinese.yml` |

---

## 实施任务

### Task 1: 验证当前所有 sl_ 文件清单

- [ ] **Step 1: 检索所有 sl_ 前缀文件**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"
find Starlike -name "sl_*.txt" -o -name "sl_*.yml" | sort
```

Expected: 输出完整清单，与上方映射表对照，缺口记录

- [ ] **Step 2: 提交验证结果**

```bash
git status --short > /tmp/sl_files_before.txt
cat /tmp/sl_files_before.txt
```

---

### Task 2: 执行代码文件重命名（1-29）

- [ ] **Step 1: 批量 git mv 代码文件**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"

git mv Starlike/common/governments/civics/000_sl_origin.txt Starlike/common/governments/civics/000_sl_copan_origin.txt
git mv Starlike/common/governments/civics/sl_civics.txt Starlike/common/governments/civics/sl_copan_civics.txt
git mv Starlike/common/governments/councilors/sl_councilors.txt Starlike/common/governments/councilors/sl_copan_councilors.txt
git mv Starlike/common/council_agendas/sl_council_agendas.txt Starlike/common/council_agendas/sl_copan_council_agendas.txt
git mv Starlike/common/static_modifiers/sl_modifiers.txt Starlike/common/static_modifiers/sl_copan_modifiers.txt
git mv Starlike/common/traditions/sl_traditions_huisu.txt Starlike/common/traditions/sl_copan_traditions_huisu.txt
git mv Starlike/common/traditions/sl_traditions_martis.txt Starlike/common/traditions/sl_copan_traditions_martis.txt
git mv Starlike/common/traditions/sl_traditions_phoenix.txt Starlike/common/traditions/sl_copan_traditions_phoenix.txt
git mv Starlike/common/traditions/sl_tradition_categories.txt Starlike/common/traditions/sl_copan_tradition_categories.txt
git mv Starlike/common/ascension_perks/00_sl_ascension.txt Starlike/common/ascension_perks/00_sl_copan_ascension.txt
git mv Starlike/common/anomalies/sl_anomalies.txt Starlike/common/anomalies/sl_copan_anomalies.txt
git mv Starlike/common/decisions/sl_decisions.txt Starlike/common/decisions/sl_copan_decisions.txt
git mv Starlike/common/edicts/sl_edicts.txt Starlike/common/edicts/sl_copan_edicts.txt
git mv Starlike/common/armies/sl_armies.txt Starlike/common/armies/sl_copan_armies.txt
git mv Starlike/common/event_chains/sl_event_chains.txt Starlike/common/event_chains/sl_copan_event_chains.txt
git mv Starlike/common/message_types/sl_messages.txt Starlike/common/message_types/sl_copan_messages.txt
git mv Starlike/common/start_screen_messages/sl_start_screen.txt Starlike/common/start_screen_messages/sl_copan_start_screen.txt
git mv Starlike/common/game_concepts/sl_concepts.txt Starlike/common/game_concepts/sl_copan_concepts.txt
git mv Starlike/common/strategic_resources/sl_strategic_resources.txt Starlike/common/strategic_resources/sl_copan_strategic_resources.txt
git mv Starlike/common/technology/sl_technologies.txt Starlike/common/technology/sl_copan_technologies.txt
git mv Starlike/common/buildings/sl_buildings.txt Starlike/common/buildings/sl_copan_buildings.txt
git mv Starlike/common/pop_jobs/sl_jobs.txt Starlike/common/pop_jobs/sl_copan_jobs.txt
git mv Starlike/common/scripted_triggers/sl_triggers.txt Starlike/common/scripted_triggers/sl_copan_triggers.txt
git mv Starlike/common/scripted_effects/sl_effects.txt Starlike/common/scripted_effects/sl_copan_effects.txt
git mv Starlike/common/situations/sl_situations.txt Starlike/common/situations/sl_copan_situations.txt
git mv Starlike/common/policies/sl_policies.txt Starlike/common/policies/sl_copan_policies.txt
git mv Starlike/common/pop_faction_types/sl_factions.txt Starlike/common/pop_faction_types/sl_copan_factions.txt
git mv Starlike/events/sl_homing_events.txt Starlike/events/sl_copan_homing_events.txt
git mv Starlike/events/sl_bloodline_events.txt Starlike/events/sl_copan_bloodline_events.txt
```

Expected: 所有 git mv 成功，无报错

- [ ] **Step 2: 验证 rename 结果**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"
find Starlike -name "sl_*.txt" | sort
```

Expected: 应只剩下 `Homing_initializers.txt`（系统初始化，非 sl_ 前缀）和 `1.txt`（on_actions）

---

### Task 3: 执行本地化文件重命名（30-45）

- [ ] **Step 1: 批量 git mv 本地化文件**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"

git mv Starlike/localisation/l_english/sl_l_english.yml Starlike/localisation/l_english/sl_copan_l_english.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese.yml
git mv Starlike/localisation/l_english/sl_l_english_events.yml Starlike/localisation/l_english/sl_copan_l_english_events.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_events.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_events.yml
git mv Starlike/localisation/l_english/sl_l_english_governments.yml Starlike/localisation/l_english/sl_copan_l_english_governments.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_governments.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_governments.yml
git mv Starlike/localisation/l_english/sl_l_english_traits.yml Starlike/localisation/l_english/sl_copan_l_english_traits.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_traits.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_traits.yml
git mv Starlike/localisation/l_english/sl_l_english_messages.yml Starlike/localisation/l_english/sl_copan_l_english_messages.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_messages.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_messages.yml
git mv Starlike/localisation/l_english/sl_l_english_economy.yml Starlike/localisation/l_english/sl_copan_l_english_economy.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_economy.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_economy.yml
git mv Starlike/localisation/l_english/sl_l_english_factions.yml Starlike/localisation/l_english/sl_copan_l_english_factions.yml
git mv Starlike/localisation/l_simp_chinese/sl_l_simp_chinese_factions.yml Starlike/localisation/l_simp_chinese/sl_copan_l_simp_chinese_factions.yml
git mv Starlike/localisation/l_english/sl_name_lists_copan_l_english.yml Starlike/localisation/l_english/sl_copan_name_lists_l_english.yml
git mv Starlike/localisation/l_simp_chinese/sl_name_lists_copan_l_simp_chinese.yml Starlike/localisation/l_simp_chinese/sl_copan_name_lists_l_simp_chinese.yml
```

- [ ] **Step 2: 验证本地化 rename 结果**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"
find Starlike/localisation -name "sl_*.yml" | sort
```

Expected: 应只剩下非 sl_ 前缀的 `l_english.yml` 和 `l_simp_chinese.yml`（游戏默认）

---

### Task 4: 全面验证

- [ ] **Step 1: 确认无遗漏 sl_ 文件**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"
find Starlike -name "sl_*" | sort
```

Expected: 仅 `Starlike/common/name_lists/00_SL_COPAN.txt`（名称表文件，非 sl_ 前缀，是 SL_ 大写）和 `Starlike/common/solar_system_initializers/Homing_initializers.txt`（系统初始化）

- [ ] **Step 2: 检查 git status**

Run:
```bash
cd "C:/Users/renow/Desktop/Starlike_beta"
git status
```

Expected: 所有变更均为 renamed，无 untracked 的 sl_ 文件

- [ ] **Step 3: 提交所有重命名**

```bash
git add -A
git commit -m "$(cat <<'EOF'
refactor: rename sl_ → sl_copan_ for all mod files

全面重命名标识一体汉和核心阵营代码，为 2.0 多阵营扩展铺底
共重命名 45 个文件（29 个代码文件 + 16 个本地化文件）
EOF
)"
```

---

## 保留文件清单（不重命名）

| 文件 | 原因 |
|---|---|
| `Starlike.mod` | 模组描述符 |
| `Starlike/descriptor.mod` | 模组描述符 |
| `Starlike/music/` | 音乐资源，用户确认不修改 |
| `Starlike/gfx/` | 图形资源 |
| `Starlike/common/solar_system_initializers/Homing_initializers.txt` | 系统初始化，与阵营无关 |
| `Starlike/common/on_actions/1.txt` | 游戏接口钩子，非 sl_ 前缀 |
| `Starlike/common/species/00_sl_species.txt` | 物种模板，需单独评估 |
| `Starlike/common/traits/00_sl_leader_traits.txt` | 领袖特质，需单独评估 |
| `Starlike/common/traits/01_sl_species_traits.txt` | 物种特质，需单独评估 |
| `Starlike/common/name_lists/00_SL_COPAN.txt` | 名称表，SL_ 大写，非 sl_ 小写前缀 |

---

## 验证清单

- [ ] `find Starlike -name "sl_*.txt"` 无输出（除 Homing_initializers.txt 外）
- [ ] `find Starlike -name "sl_*.yml"` 无输出
- [ ] `git log --oneline` 包含 refactor commit
- [ ] 所有 45 个文件均已重命名
