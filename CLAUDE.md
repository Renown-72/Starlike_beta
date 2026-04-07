# CLAUDE.md — 偌星：归巢计划

## 项目概述

Stellaris v4.3 游戏模组，采用 Paradox Scripting Language（`.txt`）全量开发，无构建系统，无测试管线，由游戏引擎直接加载。

**Steam Workshop ID**: 3480510700

**核心玩法**：`origin_homing`（归巢计划）—— Sol 系毁灭后的火星遗民文明，3 个派系国策，4 事件叙事链，25 首原声，汉和物种模板与专属名称表。

## 文件加载与命名

Stellaris 按**字母序**加载各 `common/` 子目录下的 `.txt` 文件。本模组命名策略：
- `sl_*.txt` — 实际文件前缀（如 `sl_origin.txt`、`sl_civics.txt`）
- `00_*.txt` / `1.txt` — 显式数字前缀控制加载顺序

所有 `.txt` 文件路径统一在 `Starlike/` 子目录下。

## 架构依赖链

```
Starlike.mod
  → common/on_actions/1.txt → event_homing.1
      → auto-colonize Europa → Homing_Earth_flag survey
      → anomaly_homing_earth_stardust → event_homing.2
          → event_homing.3 → event_homing.4

origin_homing (common/governments/civics/sl_origin.txt)
  ├─ initializers = { homing_earth }
  └─ possible = { }  无 authority 限制，兼容 vanilla 独裁制
      ├─ civic_huisu        → councilor_huisu  （博物传承派）
      ├─ civic_martis       → councilor_martis （荧惑科学派）
      └─ civic_phoenix_plume → councilor_phoenix_plume （鸾羽军事派）
```

**关键不变式**：所有派系国策和内阁职位均通过 `origin = { value = origin_homing }` 限制，新增内容必须保留此 gate。

## 跨文件通信 — Flag 系统

| Flag | 设置位置 | 读取位置 | 用途 |
|------|---------|---------|------|
| `Homing_Sol` | Homing_initializers.txt（系统） | event_homing.1 | 标识起始星系 |
| `Homing_Earth_flag` | Homing_initializers.txt（星球） | sl_anomalies.txt | 触发星尘异常 |
| `Homing_Europa` | Homing_initializers.txt（星球） | event_homing.1 | 标识欧罗巴自动殖民 |
| `homing_earth_investigating` | event_homing.2（3650天） | event_homing.3 触发 | 调查进行中 |
| `homing_earth_analyzed` | event_homing.3 | event_homing.4 触发 | 分析完成 |
| `homing_earth_legacy_unlocked` | event_homing.4 | event_homing.4 自身 | 防重复触发 |

重命名或删除任何 flag 必须同步更新全部引用。

## 太阳系初始状态

`common/solar_system_initializers/Homing_initializers.txt` 定义 Sol 系：
- Sol（Homing_Sol）：双恒星（Sol + 星尘化地球）
- Mars（开局星球）：`starting_planet = yes`
- 欧罗巴（Homing_Europa）：`pc_ocean`，自动殖民目标
- 木星（含 Io/Europa/Ganymede/Callisto）、土星（含 Titan）、天王星、海王星、冥王星、小行星带

## 本地化工作流

两个文件必须同步更新：
- `Starlike/localisation/l_english/sl_l_english.yml`
- `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml`

格式：
```yaml
l_english:
  key: "值 with §Hcolor codes§! and \nnewlines"
l_simp_chinese:
  key: "中文值"
```
颜色码：`§g`绿 `§H`黄 `§R`红 `§!`重置。修正器图标：`£mod_name£`。

## 版本与描述文件

- `Starlike.mod`（仓库根）：`version="4.3"` — Paradox Launcher 使用
- `Starlike/descriptor.mod`（模组内）：`version="4.3"` — Steam Workshop 上传用

两者需保持同步，`supported_version="v4.3.*"` 一致。

## 标识符前缀规范

| 前缀 | 用途 |
|------|------|
| `homing_` | 系统级 flag、初始化器 |
| `civic_` | 国策 |
| `councilor_` | 内阁职位 |
| `council_agenda_agenda_` | 内阁议程 |
| `event_homing.*` | 事件（namespace: event_homing） |
| `anomaly_homing_` | 异常 |
| `trait_scientist_stellarite_touched` | 领袖特质 |
| `trait_stellarite_` | 物种特质 |
| `homing_earth_` | 修正器 |
| `sl_` | 文件名前缀、名称表 ID（SL_COPAN） |

## 核心模块

| 模块 | 路径 | 内容 |
|------|------|------|
| 起源+国策 | `common/governments/civics/sl_origin.txt` + `sl_civics.txt` | origin_homing + 3派系国策 |
| 内阁职位 | `common/governments/councilors/sl_councilors.txt` | 3派系内阁 |
| 内阁议程 | `common/council_agendas/sl_council_agendas.txt` | 3派系议程 |
| 太阳系 | `common/solar_system_initializers/Homing_initializers.txt` | Sol 系 |
| 修正器 | `common/static_modifiers/sl_modifiers.txt` | homing_earth_memories、homing_legacy |
| 异常 | `common/anomalies/sl_anomalies.txt` | anomaly_homing_earth_stardust |
| 领袖特质 | `common/traits/00_sl_leader_traits.txt` | trait_scientist_stellarite_touched |
| 物种特质 | `common/traits/01_sl_species_traits.txt` | trait_stellarite_adapted、trait_stellarite_touched |
| 物种模板 | `common/species/00_sl_species.txt` | species_template homing_han |
| 名称表 | `common/name_lists/00_SL_COPAN.txt` | SL_COPAN 名称表 |
| 触发器 | `common/on_actions/1.txt` | on_game_start_country 钩子 |
| 事件 | `events/sl_homing_events.txt` | 4事件叙事链（namespace: event_homing） |
| 界面 | `interface/sl_origin.gfx` | GFX_origin_project_homing |
| 图形 | `gfx/` | 加载图 DDS + 起源 DDS |
| 本地化 | `localisation/` | 英中双语完整覆盖 |
| 音乐 | `music/` | 25首原声（StarlikeOST.asset） |

## 验证方式

无自动化测试，全部手动验证。通过 Stellaris 启动器加载模组测试。

控制台命令（`~` 键）：
```
event event_homing.1  # 自动殖民欧罗巴
event event_homing.2  # 星尘故土（需科学船 scope）
event event_homing.3  # 回响家园
event event_homing.4  # 恒星回响
observe                # 切换观察者模式
research_all_technologies  # 解锁全部科技
```

错误日志：`%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log`

常见错误：本地化 key 缺失（游戏内显示原始 key 名）、修正器名称未定义（静默失败）、initializer 与事件间 flag 名称不一致。

## 版本路线图

| 版本 | 主题 | 状态 |
|------|------|------|
| v0.3 | Bug修复 + 3个内阁议程 | 已完成 |
| v0.4 | 传统树 + 飞升 + 派系事件链 | 已有详细设计 |
| v0.5 | 物种预设 + 命名列表 + 舰船设计 | 已有详细设计 |
| v0.6 | 起源2：赛琳娜之耀（光海泽生） | 规划中 |
| v0.7 | 起源3：圣瞳会（卡诺莎选民） | 规划中 |
| v0.8 | 起源4：芒廷帕斯联合会（恶意收购） | 规划中 |
| v0.9 | 起源5：沙顿穆恩帝国（即见征服） | 规划中 |
| v1.0 | 共享基础设施 + 跨文明交互 | 规划中 |

详见：`docs/superpowers/plans/2026-04-05-v06-v10-origin-expansion-roadmap.md`

## Paradox 脚本语法

### 四种语言

| 扩展名 | 语言 | 用途 |
|--------|------|------|
| `.txt` `.gfx` `.gui` | Paradox Script | 游戏脚本 |
| `.yml` | Paradox Localisation | 本地化文本 |
| `.cwt` | CWT | IDE 规则文件（插件用） |
| `.csv` | Paradox CSV | 分号分隔数据 |

### Paradox Script 基本语法

**分隔符**：`=`（赋值）、`!=` 或 `<>`（不等于）、`<` `>` `<=` `>=`（比较）、`?=`（安全赋值，仅目标不存在时赋值）

**值类型**：布尔（`yes`/`no`）、整数、浮点数、字符串（可引号）、**颜色**（`rgb { 255 128 0 }` / `hsv { 0.5 0.8 1.0 }`）、块、封装变量引用、内联数学表达式

**键的限制**：未加引号的键不能包含 `@ # $ = < > ! ? { } [ ] "` 及空白字符。

```paradox
enabled = yes
level >= 2
size ?= @my_var
color = rgb { 142 188 241 }
```

### 封装变量与内联数学

```paradox
@base_cost = 100
@bonus_factor = 1.5
energy = @[ base_cost * bonus_factor ]
```

- 声明：`@<name> = <value>`，引用：`@<name>`（独立使用时带 `@`，内联数学中不带）
- 内联数学：`@[ expr ]`，支持 `+ - * / % |abs| (expr)`

### 参数与参数条件块

- 参数语法：`$name$` 或 `$name|default_value$`
- 参数条件块：`[[PARAM] members...]`（PARAM 存在时包含）、`[[!PARAM] members...]`（PARAM 不存在时包含）
- 内联参数条件：`"prefix[[PARAM]_$PARAM$]_suffix"`

```paradox
set_variable = { which = research_$category$ value = $amount|10$ }
[[!skip_notification] create_message = { type = "done" }]
```

### 本地化富文本

`l_english:` / `l_simp_chinese:` 为语言标识行，文件必须 **UTF-8 WITH BOM** 编码。

| 语法 | 说明 |
|------|------|
| `§R红§!` `§g绿§!` `§H黄§!` | 颜色标记，嵌套可用 |
| `£icon£` `£icon|frame£` | 图标，`£sl_xxx£` 对应 GFX sprite |
| `$KEY$` `$KEY|arg$` | 参数，引用其他本地化 key 或脚本变量 |
| `[Root.GetName]` | 命令，调用作用域链和 Get 方法 |
| `['concept']` `['civic:civic_name']` | 概念命令（Stellaris 特有），链接到概念定义 |
| `\$` `\\` `\n` | 转义：富文本标记、反斜杠、换行 |
| `[[` | 转义字面量 `[` |

### Paradox CSV 格式

列以分号 `;` 分隔，注释以 `#` 开头。

```csv
# Unit definitions
id;name;number;status;
some_id;some_name;0;yes;
```

## Common 子目录索引

| 目录 | 用途 |
|------|------|
| `anomalies/` | 异常事件 |
| `archaeological_site_types/` | 坟墓事件 |
| `ascension_perks/` | 飞升 |
| `astral_rifts/` | 裂隙事件 |
| `buildings/` | 行星建筑 |
| `bypass/` | 星门和虫洞 |
| `casus_belli/` | 宣战理由 |
| `colony_types/` | 殖民地类型（农业、铸造等） |
| `component_templates/` | 舰船武器 |
| `council_agendas/` | 内阁议程 |
| `decisions/` | 星球决议 |
| `defines/` | 默认数值（舰船上限、事件等待时间等） |
| `deposits/` | 行星地块 |
| `edicts/` | 法令 |
| `espionage_operation_types/` | 间谍行动类型 |
| `ethic_categories/` | 思潮类别 |
| `event_chains/` | 事件链 |
| `federation_types/` | 联邦类型 |
| `first_contact/` | 首次联系 |
| `game_concepts/` | 游戏文本特殊标记 |
| `global_ship_designs/` | 全局舰船设计（给事件用，不可编辑） |
| `governments/` | 国策、起源、政体、内阁 |
| `map_modes/` | 地图模式 |
| `megastructures/` | 巨构 |
| `message_types/` | 消息类型 |
| `name_lists/` | 名称表（帝国、物种等） |
| `on_actions/` | 游戏接口钩子（on_game_start 等） |
| `opinion_modifiers/` | 关系修正 |
| `planet_classes/` | 行星与恒星类别 |
| `policies/` | 帝国政策 |
| `pop_jobs/` | 岗位 |
| `pop_faction_types/` | 人口派系 |
| `portrait_categories/` `portrait_sets/` | 物种肖像注册 |
| `relics/` | 遗珍 |
| `script_values/` | 脚本值 |
| `scripted_effects/` | 脚本效果 |
| `scripted_loc/` | 脚本化本地化 |
| `scripted_modifiers/` | 脚本化修正器 |
| `scripted_triggers/` | 脚本化条件 |
| `scripted_variables/` | 变量脚本 |
| `ship_sizes/` | 舰船型号 |
| `situations/` | 局势 |
| `solar_system_initializers/` | 星系初始化器 |
| `special_projects/` | 特殊项目 |
| `species_archetypes/` | 物种原型类别 |
| `species_classes/` | 物种注册 |
| `species_names/` | 物种名称 |
| `species_rights/` | 物种权力政策 |
| `static_modifiers/` | 静态效果修正器 |
| `strategic_resources/` | 战略资源 |
| `technology/` | 科技 |
| `terraform/` | 星球改造 |
| `tradition_categories/` `traditions/` | 传统树 |
| `traits/` | 物种特质与领袖特质 |
| `war_goals/` | 宣战目标 |
| `ambient_objects/` | 星系环境特效 |
| `armies/` | 陆军 |
| `artifact_actions/` | 文物按钮 |
| `bombardment_stances/` | 轨道轰炸姿态 |
| `component_sets/` `component_tags/` | 舰船部件注册 |
| `country_limits/` | 国家舰船数量上限 |
| `country_types/` | 国家类型（堕落、镜像等） |
| `crisis_levels/` `crisis_objectives/` `crisis_paths/` | 天灾设定 |
| `districts/` | 区划 |
| `galactic_community_actions/` | 银河社区行动 |
| `starbase_buildings/` `starbase_levels/` `starbase_modules/` `starbase_types/` | 恒星基地 |
| `start_screen_messages/` | 开局帝国介绍 |
| `storm_types/` | 风暴 |
| `system_tooltips/` | 星系提示 |
| `technology_ages/` | 技术时代 |
| `trade_conversions/` | 贸易转换 |
| `observation_station_missions/` | 观测站任务 |

## 参考文档

`代码参考/` 目录含教程示例：
- `P语言语法参考.md` — Paradox Language Support 插件语法参考（权威）
- `P语言教程.txt` — 基础教程
- `群星数值注释.txt` — 数值参考
- `群星common文件夹下的文件的作用.txt` — 简版 common 目录说明
- `群星里的部分条件.txt` — 条件/触发器参考

子目录：起源编写/、国策和配套的领袖编写/、传统和议程的编写/、政体的编写/、科技的编写/、自建物种代码/、桌面图加载图和音乐集/

### IDE 支持

推荐安装 **Paradox Language Support**（VS Code 插件），它提供：
- 四种语言的语法高亮、补全、代码检查
- CWT 规则文件（`.cwt`）—— 相当于 JSON Schema，为脚本提供类型检查
- 本地化图标解析：自动匹配 `GFX_text_`/`GFX_` 前缀 sprite 或 `gfx/interface/icons/` 下的图片

## 常见开发任务

### 新增派系国策
1. 在 `common/governments/civics/sl_civics.txt` 添加定义块
2. `possible = { origin = { value = origin_homing } }` 限制
3. 在两个 YAML 文件添加本地化：`civic_xxx`、`civic_xxx_desc`、`civic_xxx_effects`、`civic_xxx_negative_effects`
4. 如需内阁职位，在 `common/governments/councilors/sl_councilors.txt` 添加，`civic = civic_xxx`
5. 如需内阁议程，在 `common/council_agendas/sl_council_agendas.txt` 添加，`potential = { owner = { has_civic = civic_xxx } }`

### 扩展事件链
1. 所有事件使用 `namespace = event_homing`（文件顶部声明）
2. 下一个可用 ID：`event_homing.5`
3. 添加本地化：`event_homing.N.title`、`event_homing.N.desc`、各 `event_homing.N.opt.*`
4. 通过 `trigger_event = { id = event_homing.N days = X }` 链式触发

### 新增修正器
1. 在 `common/static_modifiers/sl_modifiers.txt` 定义，附带 `icon` 引用
2. 在事件中通过 `add_modifier = { modifier = "modifier_id" days = -1 }` 应用（-1=永久）
3. 添加 `modifier_id` 和 `modifier_id_desc` 本地化 key

### 新增物种/名称表
1. 物种特质：`common/traits/01_sl_species_traits.txt`
2. 物种模板：`common/species/00_sl_species.txt`
3. 名称表：`common/name_lists/00_SL_COPAN.txt`（扩展现有或新建）
4. 同步英中双语本地化
