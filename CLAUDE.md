# CLAUDE.md — 偌星：归巢计划

Stellaris v4.3 模组，Paradox Script（`.txt`）全量开发，无构建系统，无测试管线。
**Steam Workshop ID**: 3480510700
**核心玩法**：`origin_homing`——Sol 系毁灭后的火星遗民，3 派系国策，4 事件叙事链，25 首原声，汉和物种。

## 文件加载与命名

Stellaris 按**字母序**加载 `common/` 下 `.txt` 文件。本模组：`sl_*.txt` 为文件前缀，`00_*.txt` / `1.txt` 控制加载顺序。所有文件在 `Starlike/` 子目录下。

## 架构依赖链

```
Starlike.mod → common/on_actions/1.txt → event_homing.1
  → auto-colonize Europa → Homing_Earth_flag survey
  → anomaly_homing_earth_stardust → event_homing.2 → event_homing.3 → event_homing.4

origin_homing (common/governments/civics/sl_origin.txt)
  ├─ initializers = { homing_earth }, possible = { } # 无 authority 限制，兼容 vanilla 独裁制
  ├─ civic_huisu        → councilor_huisu   （博物传承派）
  ├─ civic_martis       → councilor_martis  （荧惑科学派）
  └─ civic_phoenix_plume → councilor_phoenix_plume（鸾羽军事派）
```

**关键不变式**：所有派系国策和内阁职位通过 `origin = { value = origin_homing }` 限制，新增必须保留此 gate。

## Flag 系统

| Flag | 设置 | 读取 | 用途 |
|------|------|------|------|
| `Homing_Sol` | Homing_initializers.txt（系统） | event_homing.1 | 标识起始星系 |
| `Homing_Earth_flag` | Homing_initializers.txt（星球） | sl_anomalies.txt | 触发星尘异常 |
| `Homing_Europa` | Homing_initializers.txt（星球） | event_homing.1 | 欧罗巴自动殖民 |
| `homing_earth_investigating` | event_homing.2（3650天） | event_homing.3 | 调查进行中 |
| `homing_earth_analyzed` | event_homing.3 | event_homing.4 | 分析完成 |
| `homing_earth_legacy_unlocked` | event_homing.4 | event_homing.4 自身 | 防重复触发 |

重命名/删除 flag 必须同步全部引用。

## 太阳系初始状态

`common/solar_system_initializers/Homing_initializers.txt` 定义 Sol 系：Sol（Homing_Sol）含星尘化地球，Mars 为开局星球（`starting_planet = yes`），欧罗巴（Homing_Europa，`pc_ocean`）为自动殖民目标，含木星（含 Io/Europa/Ganymede/Callisto）、土星（含 Titan）、天王星、海王星、冥王星、小行星带。

## 本地化

两文件必须同步：`localisation/l_english/sl_l_english.yml` + `localisation/l_simp_chinese/sl_l_simp_chinese.yml`，**UTF-8 WITH BOM** 编码。

富文本标记：`§R红§! §g绿§! §H黄§!`（颜色）、`£sl_xxx£`（图标，对应 GFX sprite）、`$KEY$`（参数/本地化引用）、`[Root.GetName]`（命令）、`['concept']`（Stellaris 概念）、`\$` / `[[`（转义）。

## 版本与描述

`Starlike.mod`（根目录）和 `Starlike/descriptor.mod`（模组内）均需 `version="4.3"`、`supported_version="v4.3.*"` 保持同步。

## 标识符前缀

`homing_`（flag/初始化器）、`civic_`（国策）、`councilor_`（内阁）、`council_agenda_`（议程）、`event_homing.*`（事件）、`anomaly_homing_`（异常）、`trait_scientist_`/`trait_stellarite_`（特质）、`homing_earth_`（修正器）、`sl_`（文件前缀/名称表）

## 核心模块

| 模块 | 路径 | 内容 |
|------|------|------|
| 起源+国策 | `common/governments/civics/sl_origin.txt` + `sl_civics.txt` | origin_homing + 3 派系国策 |
| 内阁职位 | `common/governments/councilors/sl_councilors.txt` | 3 派系内阁 |
| 内阁议程 | `common/council_agendas/sl_council_agendas.txt` | 3 派系议程 |
| 太阳系 | `common/solar_system_initializers/Homing_initializers.txt` | Sol 系 |
| 修正器 | `common/static_modifiers/sl_modifiers.txt` | homing_earth_memories、homing_legacy |
| 异常 | `common/anomalies/sl_anomalies.txt` | anomaly_homing_earth_stardust |
| 领袖特质 | `common/traits/00_sl_leader_traits.txt` | trait_scientist_stellarite_touched |
| 物种特质 | `common/traits/01_sl_species_traits.txt` | trait_stellarite_adapted、trait_stellarite_touched |
| 物种模板 | `common/species/00_sl_species.txt` | species_template homing_han |
| 名称表 | `common/name_lists/00_SL_COPAN.txt` | SL_COPAN 名称表 |
| 触发器 | `common/on_actions/1.txt` | on_game_start_country 钩子 |
| 事件 | `events/sl_homing_events.txt` | 4 事件叙事链（namespace: event_homing） |
| 界面/图形 | `interface/sl_origin.gfx` + `gfx/` | GFX_origin_project_homing + 加载图 DDS |
| 本地化 | `localisation/` | 英中双语 |
| 音乐 | `music/` | 25 首原声（StarlikeOST.asset） |

## 验证

无自动化测试，手动验证。控制台命令（`~`）：
```
event event_homing.1   # 自动殖民欧罗巴
event event_homing.2   # 星尘故土（需科学船 scope）
event event_homing.3   # 回响家园
event event_homing.4   # 恒星回响
observe
research_all_technologies
```
错误日志：`%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log`
常见错误：本地化 key 缺失、修正器名称未定义（静默失败）、flag 名称不一致。

## 版本路线图

| 版本 | 主题 | 状态 |
|------|------|------|
| v0.3 | Bug修复 + 3个内阁议程 | 已完成 |
| v0.4 | 传统树 + 飞升 + 派系事件链 | 已有详细设计 |
| v0.5 | 物种预设 + 命名列表 + 舰船设计 | 已有详细设计 |
| v0.6 | 起源2：赛琳娜之耀 | 规划中 |
| v0.7 | 起源3：圣瞳会 | 规划中 |
| v0.8 | 起源4：芒廷帕斯联合会 | 规划中 |
| v0.9 | 起源5：沙顿穆恩帝国 | 规划中 |
| v1.0 | 共享基础设施 + 跨文明交互 | 规划中 |

详见：`docs/superpowers/plans/2026-04-05-v06-v10-origin-expansion-roadmap.md`

## Paradox 脚本语法

**分隔符**：`=` `!=`/`<>` `<` `>` `<=` `>=` `?=`

**值类型**：布尔（`yes`/`no`）、整数、浮点数、字符串（可引号）、颜色（`rgb { 255 128 0 }`）、块

**封装变量**：`@<name> = <value>`，引用 `@<name>`，内联数学 `@[ base_cost * bonus_factor ]`（支持 `+ - * / % |expr| (expr)`）

**参数**：`$name$` / `$name|default$`，参数条件块 `[[PARAM] members...]` / `[[!PARAM] members...]`

**键限制**：未引号键不能含 `@ # $ = < > ! ? { } [ ] "` 及空白。

**本地化语法**：文件 `.yml`，`l_english:` / `l_simp_chinese:` 语言标识行，`§R红§!` 颜色，`£icon£` 图标，`$KEY$` 参数，`[Root.GetName]` 命令，`['concept']` 概念命令，`\$`/`\\`/`\n`/`[[` 转义。

**CSV**：分号分隔，注释 `#`

IDE 推荐：**Paradox Language Support**（VS Code），CWT 规则文件提供类型检查。

## Common 目录索引（高频）

`anomalies/` 异常、`archaeological_site_types/` 坟墓、`ascension_perks/` 飞升、`buildings/` 建筑、`bypass/` 星门虫洞、`casus_belli/` 宣战理由、`colony_types/` 殖民地类型、`component_templates/` 舰船武器、`council_agendas/` 议程、`decisions/` 决议、`deposits/` 地块、`edicts/` 法令、`espionage_operation_types/` 间谍行动、`event_chains/` 事件链、`federation_types/` 联邦、`governments/` 国策/起源/政体/内阁、`megastructures/` 巨构、`name_lists/` 名称表、`on_actions/` 接口钩子、`pop_jobs/` 岗位、`pop_faction_types/` 人口派系、`relics/` 遗珍、`scripted_effects/` 效果、`scripted_triggers/` 条件、`ship_sizes/` 舰船型号、`situations/` 局势、`solar_system_initializers/` 星系、`special_projects/` 特殊项目、`static_modifiers/` 修正器、`strategic_resources/` 战略资源、`technology/` 科技、`traditions/` 传统、`traits/` 特质

## 参考文档

`代码参考/P语言语法参考.md` — 权威语法参考（必读）

## 常见开发任务

### 新增派系国策
1. `common/governments/civics/sl_civics.txt` 添加定义块，`possible = { origin = { value = origin_homing } }`
2. 双语 YAML：`civic_xxx`、`civic_xxx_desc`、`civic_xxx_effects`、`civic_xxx_negative_effects`
3. 需内阁职位：`common/governments/councilors/sl_councilors.txt` 添加，`civic = civic_xxx`
4. 需内阁议程：`common/council_agendas/sl_council_agendas.txt` 添加，`potential = { owner = { has_civic = civic_xxx } }`

### 扩展事件链
1. `namespace = event_homing`（文件顶部声明），下一个可用 ID：`event_homing.5`
2. 本地化：`event_homing.N.title`、`event_homing.N.desc`、各 `event_homing.N.opt.*`
3. `trigger_event = { id = event_homing.N days = X }` 链式触发

### 新增修正器
1. `common/static_modifiers/sl_modifiers.txt` 定义，附 `icon`
2. 事件中 `add_modifier = { modifier = "modifier_id" days = -1 }`（-1=永久）
3. YAML：`modifier_id` + `modifier_id_desc`

### 新增物种/名称表
1. 特质：`common/traits/01_sl_species_traits.txt`、模板：`common/species/00_sl_species.txt`、名称表：`common/name_lists/00_SL_COPAN.txt`
2. 同步英中双语本地化
