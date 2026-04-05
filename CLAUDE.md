# CLAUDE.md — 偌星：归巢计划

## 项目概述

Stellaris v4.3 游戏模组，采用 Paradox Scripting Language（`.txt`）全量开发，无构建系统，无测试管线，由游戏引擎直接加载。

**Steam Workshop ID**: 3480510700

**核心玩法**：origin_homing（归巢计划）—— Sol 系毁灭后的火星遗民文明，3 个派系国策，4 个事件叙事链，25 首原声。

## 架构依赖链

```
Starlike.mod → common/on_actions/1.txt → event_homing.1
  → auto-colonize Europa → Homing_Earth_flag survey
  → event_homing.2 → event_homing.3 → event_homing.4
      (flag-gated planet_event chain)

origin_homing (civics/Starlike_origin.txt)
  ├─ initializers = { homing_earth } → solar_system_initializers/Homing_initializers.txt
  └─ possible = { }  无 authority 限制，兼容 vanilla 独裁制
      ├─ civic_huisu      → councilor_huisu  （博物传承派）
      ├─ civic_martis     → councilor_martis （荧惑科学派）
      └─ civic_phoenix_plume → councilor_phoenix_plume （鸾羽军事派）
```

**关键不变式**：所有国策和内阁职位均通过 `origin = { value = origin_homing }` 限制，新增内容必须保留此 gate。

## 文件加载与命名

Stellaris 按**字母序**加载各 `common/` 子目录下的 `.txt` 文件。本模组命名策略：
- `Starlike_*.txt` — 加载于小写字母开头的 vanilla 文件之后
- `00_*.txt` / `1.txt` — 显式数字前缀控制加载顺序

**标识符前缀**：`homing_`（系统级）、`civic_`（国策）、`councilor_`（内阁）、`event_homing.*`（事件）、`trait_scientist_stellarite_touched`（特质）

## 跨文件通信 — Flag 系统

| Flag | 设置位置 | 读取位置 | 用途 |
|------|---------|---------|------|
| `Homing_Sol` | Homing_initializers.txt（系统） | event_homing.1 | 标识起始星系 |
| `Homing_Earth_flag` | Homing_initializers.txt（星球） | event_homing.2 触发 | 标识星尘化地球 |
| `Homing_Europa` | Homing_initializers.txt（星球） | event_homing.1 | 标识欧罗巴自动殖民 |
| `homing_earth_investigating` | event_homing.2（3650天） | event_homing.3 触发 | 调查进行中 |
| `homing_earth_analyzed` | event_homing.3 | event_homing.4 触发 | 分析完成 |
| `homing_earth_legacy_unlocked` | event_homing.4 | event_homing.4 自身 | 防重复触发 |

重命名或删除任何 flag 必须同步更新全部引用。

## 本地化工作流

两个文件必须同步更新：
- `Starlike/localisation/l_english/Starlike_l_english.yml`
- `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

格式：
```yaml
l_english:
  key: "值 with §Hcolor codes§! and \nnewlines"
```
颜色码：`§g`绿 `§H`黄 `§R`红 `§!`重置。修正器图标：`£mod_name£`。

## 版本与描述文件

- `Starlike.mod`（仓库根）：`version="4.3"` — Paradox Launcher 使用
- `Starlike/descriptor.mod`（模组内）：`version="4.0"` — Steam Workshop 上传用

两者需保持同步，`supported_version="v4.3.*"` 一致。

## 验证方式

无自动化测试，全部手动验证。通过 Stellaris 启动器加载模组测试。

控制台命令（~键）：
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

## 已知问题

### 已在 v0.3 规划中修复
- [ ] `civic_huisu` 的 `country_unity_produces_mult` 重复定义（0.10 被 0.15 覆盖）
- [ ] `civic_martis` 的 `science_ship_survey_speed` 重复定义（0.2 被 0.15 覆盖）
- [ ] event_homing.2 无异常定义触发（planet_event + is_triggered_only = yes 但无触发源）
- [ ] `Starlike/music/StarlikeOST.txt` 中误导性的注释 file 路径

## 版本路线图

| 版本 | 主题 | 状态 |
|------|------|------|
| v0.3 | Bug修复 + 3个内阁议程 | 已有详细设计，待执行 |
| v0.4 | 传统树 + 飞升 + 派系事件链 | 已有详细设计 |
| v0.5 | 物种预设 + 命名列表 + 舰船设计 | 已有详细设计 |
| v0.6 | 起源2：赛琳娜之耀（光海泽生） | 规划中 |
| v0.7 | 起源3：圣瞳会（卡诺莎选民） | 规划中 |
| v0.8 | 起源4：芒廷帕斯联合会（恶意收购） | 规划中 |
| v0.9 | 起源5：沙顿穆恩帝国（即见征服） | 规划中 |
| v1.0 | 共享基础设施 + 跨文明交互 | 规划中 |

详见：`docs/superpowers/plans/2026-04-05-v06-v10-origin-expansion-roadmap.md`

## 参考文档

`代码参考（必看）/` 目录含教程示例：
- `P语言教程.txt` — P 语言语法
- `群星数值注释.txt` — 数值参考
- `群星common文件夹下的文件的作用.txt` — common 子目录用途
- `群星里的部分条件.txt` — 条件/触发器参考

子目录：起源编写/、国策和配套的领袖编写/、传统和议程的编写/、政体的编写/、科技的编写/、自建物种代码/、桌面图加载图和音乐集/

## 常见开发任务

### 新增国策
1. 在 `common/governments/civics/Starlike_civics.txt` 添加定义块，`possible = { origin = { value = origin_homing } }`
2. 在两个 YAML 文件添加本地化：`civic_xxx`、`civic_xxx_desc`、`civic_xxx_effects`、`civic_xxx_negative_effects`
3. 如需内阁职位，在 `common/governments/councilors/Starlike_councilors.txt` 添加，`civic = civic_xxx`

### 扩展事件链
1. 所有事件使用 `namespace = event_homing`（文件顶部声明）
2. 下一个可用 ID：`event_homing.5`
3. 添加本地化：`event_homing.N.title`、`event_homing.N.desc`、各 `event_homing.N.opt.*`
4. 通过 `trigger_event = { id = event_homing.N days = X }` 链式触发

### 新增修正器
1. 在 `common/static_modifiers/Starlike_modifiers.txt` 定义，附带 `icon` 引用
2. 在事件中通过 `add_modifier = { modifier = "modifier_id" days = -1 }` 应用（-1=永久）
3. 添加 `modifier_id` 和 `modifier_id_desc` 本地化 key

## 模块索引

| 模块 | 路径 | 内容 |
|------|------|------|
| 起源+国策 | `common/governments/` | origin_homing + 3派系国策 + 3内阁 |
| 太阳系 | `common/solar_system_initializers/` | Sol 系（10行星+5卫星） |
| 修正器 | `common/static_modifiers/` | homing_earth_memories、homing_legacy |
| 特质 | `common/traits/` | trait_scientist_stellarite_touched |
| 触发器 | `common/on_actions/` | on_game_start_country 钩子 |
| 事件 | `events/` | 4事件叙事链（namespace: event_homing） |
| 本地化 | `localisation/` | 英中双语完整覆盖 |
| 音乐 | `music/` | 25首原声（StarlikeOST.asset） |
| 界面 | `interface/` | GFX_origin_project_homing |
| 图形 | `gfx/` | 10加载图+2起源 DDS（binary） |

## v0.3 执行入口

`docs/superpowers/plans/2026-03-31-v03-bugfix-agendas.md` — 详细执行步骤文件级。

任务清单：
1. 创建异常定义 `common/anomalies/Starlike_anomalies.txt`
2. 修复 event_homing.2 scope（planet_event→ship_event）
3. 修复 civic 重复修正器
4. 清理 music 文件 + 同步版本号
5. 创建 3 个内阁议程
6. 添加英中双语本地化
