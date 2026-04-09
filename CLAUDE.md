# CLAUDE.md — 偌星：归巢计划

Stellaris v4.3 模组，Paradox Script（`.txt`）全量开发，无构建系统，无测试管线。
**Steam Workshop ID**: 3480510700
**核心玩法**：`origin_homing`——Sol 系毁灭后的火星遗民，3 派系国策，4 事件叙事链，25 首原声，汉和物种。

## 一、核心架构

### 文件加载与命名
Stellaris 按**字母序**加载 `common/` 下 `.txt` 文件。本模组：`sl_*.txt` 为文件前缀，`00_*.txt` / `1.txt` 控制加载顺序。所有文件在 `Starlike/` 子目录下。

### 架构依赖链
```
Starlike.mod → common/on_actions/1.txt → event_homing.1
  → auto-colonize Europa → Homing_Earth_flag survey
  → anomaly_homing_earth_stardust → event_homing.2 → event_homing.3 → event_homing.4

origin_homing (common/governments/civics/sl_origin.txt)
  ├─ initializers = { homing_earth }, possible = { } # 无 authority 限制
  ├─ civic_huisu        → councilor_huisu   （博物传承派）
  ├─ civic_martis       → councilor_martis  （荧惑科学派）
  └─ civic_phoenix_plume → councilor_phoenix_plume（鸾羽军事派）
```

**关键不变式**：所有派系国策和内阁职位通过 `origin = { value = origin_homing }` 限制，新增必须保留此 gate。

### Flag 系统
| Flag | 设置 | 读取 | 用途 |
|------|------|------|------|
| `Homing_Sol` | Homing_initializers.txt（系统） | event_homing.1 | 标识起始星系 |
| `Homing_Earth_flag` | Homing_initializers.txt（星球） | sl_anomalies.txt | 触发星尘异常 |
| `Homing_Europa` | Homing_initializers.txt（星球） | event_homing.1 | 欧罗巴自动殖民 |
| `homing_earth_investigating` | event_homing.2（3650天） | event_homing.3 | 调查进行中 |
| `homing_earth_analyzed` | event_homing.3 | event_homing.4 | 分析完成 |
| `homing_earth_legacy_unlocked` | event_homing.4 | event_homing.4 自身 | 防重复触发 |

重命名/删除 flag 必须同步全部引用。

## 二、开发规范

### 命名规范
- **文件前缀**：`sl_`（缩写）
- **标识符前缀**：`homing_`（flag）、`civic_`（国策）、`councilor_`（内阁）
- **命名表**：`SL_COPAN`（统一名称表）
- **事件命名**：`event_homing.N`（namespace: event_homing）

### 本地化规范
两文件必须同步：`localisation/l_english/sl_l_english.yml` + `localisation/l_simp_chinese/sl_l_simp_chinese.yml`，**UTF-8 WITH BOM** 编码。

富文本标记：`§R红§! §g绿§! §H黄§!`（颜色）、`£sl_xxx£`（图标）、`$KEY$`（参数）、`[Root.GetName]`（命令）、`['concept']`（概念）、`\$` / `[[`（转义）。

### 版本控制
`Starlike.mod`（根目录）和 `Starlike/descriptor.mod`（模组内）均需：
- `version="4.3"`
- `supported_version="v4.3.*"`

## 三、目录结构（基于女仆模组优化）
```
Starlike/
├── descriptor.mod          # 模组描述符（必需）
├── common/                # 游戏机制定义（核心）
│   ├── # 基础框架
│   │   ├── game_concepts/         # 游戏概念
│   │   ├── script_values/         # 脚本值
│   │   └── on_actions/            # 接口钩子
│   │
│   ├── # 政治系统
│   │   ├── governments/           # 政府、国策、内阁、政体
│   │   │   ├── civics/           # 国策（派系国策）
│   │   │   ├── origins/          # 起源
│   │   │   ├── councilors/       # 内阁职位
│   │   │   └── council_agendas/  # 内阁议程
│   │   ├── traditions/           # 传统
│   │   │   ├── 000_sl_tradition_categories.txt
│   │   │   ├── sl_traditions_huisu.txt
│   │   │   ├── sl_traditions_martis.txt
│   │   │   └── sl_traditions_phoenix.txt
│   │   └── scripted_*/           # 脚本（效果、触发器、变量）
│   │
│   ├── # 经济与军事
│   │   ├── pop_jobs/             # 岗位
│   │   ├── pop_faction_types/    # 人口派系
│   │   ├── armies/               # 陆军
│   │   ├── megastructures/       # 巨构
│   │   ├── buildings/            # 建筑
│   │   ├── districts/            # 区划
│   │   ├── deposits/             # 地块
│   │   ├── decisions/            # 决议
│   │   └── edicts/               # 法令
│   │
│   ├── # 探索与事件
│   │   ├── anomalies/            # 异常
│   │   ├── archaeological_site_types/# 坟墓
│   │   ├── events/               # 事件
│   │   ├── event_chains/         # 事件链
│   │   ├── special_projects/     # 特殊项目
│   │   └── solar_system_initializers/# 星系
│   │
│   ├── # 物种与视觉
│   │   ├── traits/               # 特质（领袖/物种）
│   │   ├── species_classes/      # 物种类别
│   │   ├── species_rights/       # 物种权力
│   │   ├── name_lists/           # 名称表
│   │   ├── portrait_categories/ # 肖像类别
│   │   └── portrait_sets/       # 肖像集合
│   │
│   └── # 修正器与效果
│       ├── static_modifiers/     # 静态修正器
│       ├── scripted_modifiers/   # 脚本修正器
│       ├── message_types/       # 消息类型
│       └── modifiers/           # 游戏修正器（可扩展）
├── localisation/            # 双语本地化
│   ├── english/               # 英文本地化
│   │   ├── l_english.yml
│   │   ├── sl_l_english.yml      # 主本地化文件
│   │   ├── sl_l_english_traits.yml # 物种/特质本地化
│   │   ├── sl_l_english_events.yml # 事件本地化
│   │   ├── sl_l_english_governments.yml # 政府本地化
│   │   └── sl_l_english_messages.yml # 消息本地化
│   │
│   ├── simp_chinese/         # 简体中文本地化
│   │   ├── l_simp_chinese.yml
│   │   ├── sl_l_simp_chinese.yml
│   │   ├── sl_l_simp_chinese_traits.yml
│   │   ├── sl_l_simp_chinese_events.yml
│   │   ├── sl_l_simp_chinese_governments.yml
│   │   └── sl_l_simp_chinese_messages.yml
│   │
│   └── assets/               # 本地化资源（图标、图片等）
├── interface/               # UI界面
│   ├── sl_origin.gfx         # 起源图形定义
│   ├── custom_icons.gfx      # 自定义图标
│   └── custom_gui.gui       # 自定义界面布局
├── gfx/                    # 图形资源
│   ├── icons/               # 图标文件
│   ├── portraits/           # 肖像资源
│   ├── event_pictures/      # 事件图片
│   └── models/              # 3D模型
├── music/                  # 音效资源
│   └── StarlikeOST.asset    # 音乐资源
└── prescripted_countries/  # 预设国家（可选）
```

## 四、本地化规范（基于女仆模组）

### 目录结构
```
localisation/
├── english/                 # 英文本地化
│   ├── l_english.yml         # 游戏默认本地化
│   ├── sl_l_english.yml      # 模组主本地化文件
│   ├── sl_l_english_traits.yml # 物种/特质本地化
│   ├── sl_l_english_events.yml # 事件本地化
│   ├── sl_l_english_governments.yml # 政府本地化
│   ├── sl_l_english_messages.yml # 消息本地化
│   └── etc...               # 其他功能本地化
├── simp_chinese/            # 简体中文本地化
│   ├── l_simp_chinese.yml     # 游戏默认本地化
│   ├── sl_l_simp_chinese.yml  # 模组主本地化文件
│   ├── sl_l_simp_chinese_traits.yml # 物种/特质本地化
│   ├── sl_l_simp_chinese_events.yml # 事件本地化
│   ├── sl_l_simp_chinese_governments.yml # 政府本地化
│   └── sl_l_simp_chinese_messages.yml # 消息本地化
└── assets/                 # 本地化资源（图标、图片等）
```

### 本地化文件命名规范
- 主文件：`sl_l_{language}.yml`（如 `sl_l_english.yml`）
- 子模块：`sl_l_{language}_{feature}.yml`（如 `sl_l_english_events.yml`）
- 常量定义：`{prefix}_{name}`（如 `sl_ms_const_unity`）
- 动态文本：使用 `$KEY$` 引用
- 隐藏文本：`MS_EMPTY: " "`（用于占位）

### 本地化代码规范

**富文本标记**：
- 颜色：`§R红§! §g绿§! §H黄§!`
- 图标：`£sl_xxx£` 或 `£sl_xxx|frame£`
- 参数：`$KEY$` 或 `$KEY|format$`
- 命令：`[Root.GetName]` 或 `[Root.Owner.GetName]`
- 概念：`['concept']`
- 转义：`\$` / `[[` / `\\` / `\n`

**常量定义**（参考女仆模组）：
```yaml
# 通用常量
l_english:
    SL_PLUS: "+"
    SL_DASH: "-"
    SL_EMPTY: " "
    SL_NULL: ""
    SL_ARROW_UP: "£sl_arrow_up£"
    SL_ARROW_DOWN: "£sl_arrow_down£"
    
# 模块常量
l_english:
    # 事件提示
    EVENT_SL_TRIGGER: "触发："
    EVENT_SL_OPTION: "选择"
    
    # 数值修饰
    VALUE_POS: "§G+$VALUE$§!"
    VALUE_NEG: "§R-$VALUE$§!"
    VALUE_ZERO: "§Y$VALUE$§!"
```

**本地化最佳实践**：
1. **分离关注点**：按功能拆分文件（events、governments、traits等）
2. **统一命名**：所有键使用 `sl_` 前缀
3. **占位符**：使用 `SL_EMPTY` 或 `""` 占位
4. **参数化**：所有动态内容使用 `$KEY$`
5. **富文本**：适度使用颜色和图标增强可读性

### 女仆模组本地化技巧
1. **延迟文本**：`key_delayed` 用于解释效果触发后的变化
2. **条件文本**：`$PARAM$` 根据参数显示不同内容
3. **数值格式化**：`$VALUE|+1$` 显示带符号的数值
4. **复杂组合**：`"Prefix §G$KEY$§! Suffix"` 组合静态和动态内容
5. **概念链接**：`['concept', '说明文本']` 链接到游戏概念

### 键命名规范
- 主定义：`sl_feature_name`（如 `sl_origin_homing`）
- 描述：`sl_feature_name_desc`（如 `sl_origin_homing_desc`）
- 效果描述：`sl_effect_desc`（如 `sl_unity_bonus_desc`）
- 选项：`sl_event_name_opt_1`、`sl_event_name_opt_2`
- 消息：`sl_message_type_name`（如 `sl_notification_colony`）
- 修正器：`modifier_sl_feature_name`

## 四、核心模块

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
| 事件 | `events/sl_homing_events.txt` | 4 事件叙事链 |
| 界面/图形 | `interface/sl_origin.gfx` + `gfx/` | GFX 资源 |

## 五、验证与调试

### 控制台命令
```
event event_homing.1   # 自动殖民欧罗巴
event event_homing.2   # 星尘故土（需科学船 scope）
event event_homing.3   # 回响家园
event event_homing.4   # 恒星回响
observe
research_all_technologies
```

### 错误日志
`%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log`

### 常见错误
- 本地化 key 缺失
- 修正器名称未定义（静默失败）
- flag 名称不一致

## 六、版本路线图

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

## 七、参考文档

- `C:\Program Files (x86)\Steam\steamapps\common\Stellaris` - 原版代码
- `代码参考/P语言语法参考.md` - P语言语法参考

## 八、常见开发任务

### 新增派系国策
1. `common/governments/civics/sl_civics.txt` 添加定义块
2. 双语 YAML：`civic_xxx`、`civic_xxx_desc`、`civic_xxx_effects`、`civic_xxx_negative_effects`
3. 需内阁职位：`common/governments/councilors/sl_councilors.txt` 添加
4. 需内阁议程：`common/council_agendas/sl_council_agendas.txt` 添加

### 扩展事件链
1. `namespace = event_homing`，下一个可用 ID：`event_homing.5`
2. 本地化：`event_homing.N.title`、`event_homing.N.desc`、各选项
3. `trigger_event = { id = event_homing.N days = X }` 链式触发

### 新增修正器
1. `common/static_modifiers/sl_modifiers.txt` 定义，附 `icon`
2. 事件中 `add_modifier = { modifier = "modifier_id" days = -1 }`（-1=永久）
3. YAML：`modifier_id` + `modifier_id_desc`

### 新增物种/名称表
1. 特质：`common/traits/01_sl_species_traits.txt`
2. 模板：`common/species/00_sl_species.txt`
3. 名称表：`common/name_lists/00_SL_COPAN.txt`