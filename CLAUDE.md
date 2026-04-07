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

## 参考文档

`代码参考/` 目录含教程示例：
- `P语言教程.txt` — P 语言语法
- `群星数值注释.txt` — 数值参考
- `群星common文件夹下的文件的作用.txt` — common 子目录用途
- `群星里的部分条件.txt` — 条件/触发器参考

子目录：起源编写/、国策和配套的领袖编写/、传统和议程的编写/、政体的编写/、科技的编写/、自建物种代码/、桌面图加载图和音乐集/

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
