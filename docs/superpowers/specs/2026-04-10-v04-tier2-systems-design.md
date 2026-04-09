# v0.4 Tier 2 系统补全设计规格

> **作用域**：在 v0.4 核心内容（传统树 + 飞升 + 事件链）基础上，补全缺失的游戏系统——建筑、岗位、决议、法令、陆军、事件链追踪、游戏概念、开屏消息、消息类型、脚本基础设施。
> **前置状态**：v0.4 核心已完成（commit f23ecc3），3 棵传统树 + 4 个飞升 + events 5-16 均已提交。
> **设计方向**：方案 B（共享系统），所有新增系统以 `origin_homing` 为 gate，不按派系拆分。
> **不涉及**：GFX/DDS 图标文件创建，v0.5 世界构建内容。

---

## 一、架构决策

| 决策点 | 选定方案 | 拒绝方案 |
|--------|----------|----------|
| 建筑/岗位/陆军按派系拆分？ | **建筑按派系拆分**（帝国唯一 + civic gate），决议/法令/陆军共享 | 全部共享（方案 B 初版） |
| 图标方案 | **复用原版 GFX** 作为占位 | 创建自定义 DDS（需美术资源） |
| 事件链追踪 | **3 条 event_chain** 定义 | 无追踪（现状） |
| 脚本基础设施 | **scripted_triggers + scripted_effects** | 继续内联重复代码 |

---

## 二、模块 A：建筑（3 个 · 派系帝国建筑）

每个派系拥有一个帝国唯一建筑（`empire_limit = { base = 1 }`），由**采纳对应传统树**解锁。
传统树采纳需要对应 civic（间接 gate），但建筑本身只检查传统树状态，逻辑更清晰。

### 文件：NEW `Starlike/common/buildings/sl_buildings.txt`

### 2.1 building_sl_huisu_hall（辉夙文明殿）

```plaintext
building_sl_huisu_hall = {
    base_buildtime = 480
    category = unity
    
    empire_limit = { base = 1 }
    
    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_huisu_adopt
        }
    }
    
    allow = {
        has_major_capital = yes
    }
    
    planet_modifier = {
        pop_happiness = 0.05
    }
    
    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_huisu_adopt }
        }
        modifier = {
            job_sl_archivist_add = 3
        }
    }
    
    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            unity = 200
        }
        upkeep = {
            energy = 3
        }
    }
    
    prerequisites = { }
    
    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_huisu_adopt } }
        }
    }
}
```

**定位**：辉夙博物传承派的文明核心——保存 Sol 毁灭前后所有档案的帝国级博物馆。
**限制**：帝国唯一 + 首都限定 + `tr_huisu_adopt`（采纳辉夙传统树后解锁）。
**提供**：`sl_archivist` x 3（档案官：凝聚力 + 社会学）。

### 2.2 building_sl_martis_institute（荧惑研究所）

```plaintext
building_sl_martis_institute = {
    base_buildtime = 480
    category = research
    
    empire_limit = { base = 1 }
    
    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_martis_adopt
        }
    }
    
    allow = {
        has_major_capital = yes
    }
    
    planet_modifier = {
        planet_researchers_physics_research_produces_add = 2
    }
    
    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_martis_adopt }
        }
        modifier = {
            job_sl_researcher_add = 3
        }
    }
    
    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            alloys = 100
        }
        upkeep = {
            energy = 4
        }
    }
    
    prerequisites = { }
    
    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_martis_adopt } }
        }
    }
}
```

**定位**：荧惑科学派的帝国级研究枢纽——延续火星重建时期的尖端科学传统。
**限制**：帝国唯一 + 首都限定 + `tr_martis_adopt`（采纳荧惑传统树后解锁）。
**星球加成**：所有研究员 +2 物理学。
**提供**：`sl_researcher` x 3（荧惑研究员：物理 + 工程）。

### 2.3 building_sl_phoenix_fortress（鸾羽军事堡垒）

```plaintext
building_sl_phoenix_fortress = {
    base_buildtime = 480
    category = army
    
    empire_limit = { base = 1 }
    
    potential = {
        exists = owner
        owner = {
            has_origin = origin_homing
            has_tradition = tr_phoenix_adopt
        }
    }
    
    allow = {
        has_major_capital = yes
    }
    
    country_modifier = {
        country_naval_cap_add = 20
    }
    
    triggered_planet_modifier = {
        potential = {
            exists = owner
            owner = { has_tradition = tr_phoenix_adopt }
        }
        modifier = {
            job_sl_quartermaster_add = 3
        }
    }
    
    resources = {
        category = planet_buildings
        cost = {
            minerals = 500
            alloys = 150
        }
        upkeep = {
            energy = 3
            alloys = 1
        }
    }
    
    prerequisites = { }
    
    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            NOT = { owner = { has_tradition = tr_phoenix_adopt } }
        }
    }
}
```

**定位**：鸾羽军事派的帝国级军事指挥中枢——凤翎号战舰精神的实体化。
**限制**：帝国唯一 + 首都限定 + `tr_phoenix_adopt`（采纳鸾羽传统树后解锁）。
**帝国加成**：+20 海军容量。
**提供**：`sl_quartermaster` x 3（军需官：合金 + 海军容量）。

---

## 三、模块 B：岗位（3 个 · 对应派系建筑）

### 文件：NEW `Starlike/common/pop_jobs/sl_jobs.txt`

### 3.1 sl_archivist（档案官 · 辉夙）

```plaintext
sl_archivist = {
    category = specialist
    building_icon = building_sl_huisu_hall
    clothes_texture_index = 3
    
    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }
    
    possible_precalc = can_fill_specialist_job
    
    resources = {
        category = planet_jobs
        produces = {
            unity = 4
            society_research = 3
        }
    }
    
    triggered_planet_modifier = {
        potential = { always = yes }
        planet_amenities_add = 3
    }
    
    weight = {
        weight = @specialist_job_weight
    }
}
```

**产出**：凝聚力 4 + 社会学 3 + 舒适度 3。

### 3.2 sl_researcher（荧惑研究员 · 荧惑）

```plaintext
sl_researcher = {
    category = specialist
    building_icon = building_sl_martis_institute
    clothes_texture_index = 3
    
    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }
    
    possible_precalc = can_fill_specialist_job
    
    resources = {
        category = planet_jobs
        produces = {
            physics_research = 4
            engineering_research = 3
        }
    }
    
    weight = {
        weight = @specialist_job_weight
    }
}
```

**产出**：物理学 4 + 工程学 3。

### 3.3 sl_quartermaster（军需官 · 鸾羽）

```plaintext
sl_quartermaster = {
    category = specialist
    building_icon = building_sl_phoenix_fortress
    clothes_texture_index = 3
    
    possible_pre_triggers = {
        has_owner = yes
        is_being_purged = no
        is_being_assimilated = no
        is_sapient = yes
    }
    
    possible_precalc = can_fill_specialist_job
    
    resources = {
        category = planet_jobs
        produces = {
            alloys = 2
        }
    }
    
    country_modifier = {
        country_naval_cap_add = 4
    }
    
    weight = {
        weight = @specialist_job_weight
    }
}
```

**产出**：合金 2 + 每岗 +4 海军容量。

---

## 四、模块 C：决议（2 个）

### 文件：NEW `Starlike/common/decisions/sl_decisions.txt`

### 4.1 decision_sl_rebuild_earth（重建地球）

```plaintext
decision_sl_rebuild_earth = {
    owned_planets_only = yes
    
    resources = {
        category = decisions
        cost = {
            unity = 2000
            alloys = 500
        }
    }
    
    enactment_time = 720
    
    potential = {
        has_planet_flag = Homing_Earth_flag
        owner = {
            has_origin = origin_homing
            has_ascension_perk = ap_ranshanxia_bloodline
            has_country_flag = homing_earth_legacy_unlocked
        }
        NOT = { has_planet_flag = sl_earth_rebuilt }
    }
    
    allow = {
        custom_tooltip = {
            fail_text = "decision_sl_rebuild_earth_requirement"
            owner = { has_ascension_perk = ap_ranshanxia_bloodline }
        }
    }
    
    effect = {
        set_planet_flag = sl_earth_rebuilt
        change_pc = pc_continental
        add_modifier = { modifier = modifier_sl_rebuilt_earth days = -1 }
        owner = {
            country_event = { id = event_homing.17 days = 5 }
        }
    }
    
    ai_weight = { weight = 0 }
}
```

**叙事定位**：归巢计划的终极回报——将星尘化的故土重新变为宜居世界。
**前置**：染山霞飞升 + 完成地球遗产事件链。
**效果**：星球变为大陆型 + 永久修正 `modifier_sl_rebuilt_earth`（+20% 全资源）+ 触发庆典事件。

### 4.2 decision_sl_homing_broadcast（归巢广播）

```plaintext
decision_sl_homing_broadcast = {
    owned_planets_only = yes
    
    resources = {
        category = decisions
        cost = {
            unity = 500
        }
    }
    
    enactment_time = 180
    
    potential = {
        owner = {
            has_origin = origin_homing
            OR = {
                has_tradition = tr_huisu_finish
                has_tradition = tr_martis_finish
                has_tradition = tr_phoenix_finish
            }
        }
        NOT = { has_modifier = sl_broadcast_active }
    }
    
    effect = {
        add_modifier = { modifier = sl_broadcast_active days = 3600 }
    }
    
    ai_weight = {
        weight = 5
        modifier = {
            factor = 0
            has_modifier = sl_broadcast_active
        }
    }
}
```

**叙事定位**：向银河广播归巢文明的故事，吸引认同者加入。
**前置**：完成任一传统树。
**效果**：+25% 移民吸引力 + 5% 人口幸福度（10 年）。

---

## 五、模块 D：法令（1 个）

### 文件：NEW `Starlike/common/edicts/sl_edicts.txt`

### 5.1 edict_sl_homing_recall（归巢令）

```plaintext
edict_sl_homing_recall = {
    length = -1
    
    potential = {
        has_origin = origin_homing
        OR = {
            has_tradition = tr_huisu_finish
            has_tradition = tr_martis_finish
            has_tradition = tr_phoenix_finish
        }
    }
    
    allow = { }
    
    resources = {
        category = edicts
        upkeep = {
            unity = 10
        }
    }
    
    modifier = {
        country_unity_produces_mult = 0.05
        leader_lifespan_add = 10
        pop_happiness = 0.05
    }
    
    ai_weight = {
        weight = 5
        modifier = {
            factor = 0
            NOT = { has_origin = origin_homing }
        }
    }
}
```

**定位**：永久法令，以国家法令形式号召全民铭记归巢之路。
**前置**：完成任一传统树。
**维护**：凝聚力 10/月。
**效果**：+5% 凝聚力产出，+10 领袖寿命，+5% 人口幸福度。

---

## 六、模块 E：陆军（1 个）

### 文件：NEW `Starlike/common/armies/sl_armies.txt`

### 6.1 sl_homing_volunteer（归巢志愿军）

```plaintext
sl_homing_volunteer = {
    damage = 1.50
    health = 1.20
    morale = 2.00
    collateral_damage = 0.75
    war_exhaustion = 0.80
    time = 90
    
    icon = GFX_army_type_defense
    
    potential_country = {
        has_origin = origin_homing
    }
    
    potential = {
        from = { is_sapient = yes }
    }
    
    resources = {
        category = armies
        cost = {
            minerals = 75
        }
        upkeep = {
            food = 1
        }
    }
    
    ai_weight = {
        base = 5
        modifier = {
            factor = 0
            NOT = { owner = { has_origin = origin_homing } }
        }
    }
}
```

**定位**：不依赖重装科技的文化民兵。靠士气和归巢信念作战。
**特点**：高士气（2.0 vs 原版防御军 1.25），低附带伤害（0.75），中等攻击力。
**无科技前置**：文化陆军，开局可用。

---

## 七、模块 F：事件链定义（3 条）

### 文件：NEW `Starlike/common/event_chains/sl_event_chains.txt`

```plaintext
sl_chain_earth_legacy = {
    icon = "gfx/interface/icons/situation_log/situation_log_quest.dds"
    picture = GFX_event_archaeology
    
    counter = {
        sl_earth_legacy_progress = { max = 4 }
    }
    
    abort_trigger = {
        has_country_flag = homing_earth_legacy_unlocked
    }
}

sl_chain_faction_story = {
    icon = "gfx/interface/icons/situation_log/situation_log_quest.dds"
    picture = GFX_event_archaeology
    
    counter = {
        sl_faction_story_progress = { max = 3 }
    }
    
    abort_trigger = {
        OR = {
            has_country_flag = event_homing_5_fired
            has_country_flag = event_homing_8_fired
            has_country_flag = event_homing_11_fired
        }
        # 当 3 段链中对应的那条完成后 abort
    }
}

sl_chain_convergence = {
    icon = "gfx/interface/icons/situation_log/situation_log_precursor.dds"
    picture = GFX_event_planet_settlement
    
    counter = {
        sl_convergence_progress = { max = 3 }
    }
    
    abort_trigger = {
        has_global_flag = homing_realized
    }
}
```

**设计说明**：
- 事件链在情报日志中显示为可追踪任务，玩家可以看到进度。
- 需要在对应事件中追加 `begin_event_chain` / `add_event_chain_counter` 调用（编辑现有事件文件）。
- `abort_trigger` 在链完成后自动从日志中移除。

---

## 八、模块 G：游戏概念（6 条）

### 文件：NEW `Starlike/common/game_concepts/sl_concepts.txt`

```plaintext
concept_sl_origin_homing = {
    icon = gfx/interface/icons/concepts/concept_origins.dds
}
concept_sl_huisu = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_martis = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_phoenix = {
    icon = gfx/interface/icons/concepts/concept_civic.dds
}
concept_sl_ranshanxia = {
    icon = gfx/interface/icons/concepts/concept_trait.dds
}
concept_sl_homing_tradition = {
    icon = gfx/interface/icons/concepts/concept_traditions.dds
}
```

每个概念需要在本地化文件中定义 `concept_sl_xxx` 和 `concept_sl_xxx_desc`，hover 时弹出工具提示。

---

## 九、模块 H：开屏消息（1 条）

### 文件：NEW `Starlike/common/start_screen_messages/sl_start_screen.txt`

```plaintext
part = {
    location = 0
    localization = origins_sl_homing_start_screen
    trigger = {
        has_origin = origin_homing
    }
}
```

本地化内容将讲述归巢文明从太阳系毁灭到火星重建的开局故事，为玩家设定叙事基调。

---

## 十、模块 I：消息类型（2 个）

### 文件：NEW `Starlike/common/message_types/sl_messages.txt`

```plaintext
message_type = {
    key = MESSAGE_SL_EVENT_PROGRESS
    icon = GFX_message_quest
    icon_frame = 1
    name = MESSAGE_SL_EVENT_PROGRESS
    message_setting_key = MESSAGE_SL_EVENT_PROGRESS.s
    sound = notification
    ping = ping_notification_green
    category = science
}

message_type = {
    key = MESSAGE_SL_HOMING_MILESTONE
    icon = GFX_message_quest
    icon_frame = 1
    name = MESSAGE_SL_HOMING_MILESTONE
    message_setting_key = MESSAGE_SL_HOMING_MILESTONE.s
    sound = event_notification
    ping = ping_notification_yellow
    category = unity
}
```

---

## 十一、模块 J：脚本基础设施

### 文件：NEW `Starlike/common/scripted_triggers/sl_triggers.txt`

```plaintext
# 归巢起源检查
sl_is_homing_origin = {
    has_origin = origin_homing
}

# 任一派系传统树完成
sl_has_any_faction_tradition = {
    OR = {
        has_tradition = tr_huisu_finish
        has_tradition = tr_martis_finish
        has_tradition = tr_phoenix_finish
    }
}

# 任一派系飞升已点
sl_has_any_faction_ascendant = {
    OR = {
        has_ascension_perk = ap_huisu_ascendant
        has_ascension_perk = ap_martis_ascendant
        has_ascension_perk = ap_phoenix_ascendant
    }
}
```

### 文件：NEW `Starlike/common/scripted_effects/sl_effects.txt`

```plaintext
# 安全添加事件链进度
sl_progress_earth_chain = {
    if = {
        limit = { has_event_chain = sl_chain_earth_legacy }
        add_event_chain_counter = {
            event_chain = sl_chain_earth_legacy
            counter = sl_earth_legacy_progress
            amount = 1
        }
    }
}

sl_progress_faction_chain = {
    if = {
        limit = { has_event_chain = sl_chain_faction_story }
        add_event_chain_counter = {
            event_chain = sl_chain_faction_story
            counter = sl_faction_story_progress
            amount = 1
        }
    }
}

sl_progress_convergence_chain = {
    if = {
        limit = { has_event_chain = sl_chain_convergence }
        add_event_chain_counter = {
            event_chain = sl_chain_convergence
            counter = sl_convergence_progress
            amount = 1
        }
    }
}
```

---

## 十二、新增修正器

### 文件：EDIT `Starlike/common/static_modifiers/sl_modifiers.txt`（追加）

```plaintext
# ===== Tier 2 Modifiers · v0.4 补全 =====

# 重建地球永久修正
modifier_sl_rebuilt_earth = {
    icon = "gfx/interface/icons/modifiers/modifier_research_speed.dds"
    planet_jobs_produces_mult = 0.20
}

# 归巢广播临时修正
sl_broadcast_active = {
    icon = "gfx/interface/icons/modifiers/modifier_pop_happiness.dds"
    planet_immigration_pull_mult = 0.25
    pop_happiness = 0.05
}
```

---

## 十三、事件文件修改

### 文件：EDIT `Starlike/events/sl_homing_events.txt`（追加 1 个事件）

#### event_homing.17（重建地球庆典）

```plaintext
event_homing.17 = {
    title = "event_homing.17.title"
    desc = "event_homing.17.desc"
    
    picture = GFX_event_planet_settlement
    
    is_triggered_only = yes
    
    immediate = { }
    
    option = {
        name = "event_homing.17.opt.new_dawn"
        add_resource = { type = unity amount = 1000 }
        add_resource = { type = influence amount = 200 }
    }
}
```

**叙事**：地球重建完成，汉和文明在千年流亡后真正回到了故土。奖励凝聚力 1000 + 影响力 200。

### 现有事件修改

需要在 events 1-4, 5-16 中追加 `begin_event_chain` 和 `sl_progress_*_chain` 调用，让事件与情报日志追踪系统联动。具体改动在实现计划中逐步列出。

---

## 十四、本地化工作量

### 14.1 分类统计

| 类别 | 键数 | 说明 |
|------|------|------|
| 建筑 | 6 对 | 3 建筑 × (name + desc) |
| 岗位 | 6 对 | 3 岗位 × (name + desc) |
| 决议 | 6 对 | 2 决议 × (name + desc + tooltip) |
| 法令 | 2 对 | 1 法令 × (name + desc) |
| 陆军 | 2 对 | 1 陆军 × (name + desc) |
| 事件链 | 6 对 | 3 链 × (title + desc) |
| 游戏概念 | 12 对 | 6 概念 × (name + desc) |
| 开屏消息 | 1 对 | 1 条开屏文本 |
| 消息类型 | 4 对 | 2 类型 × (name + setting) |
| 修正器 | 4 对 | 2 修正 × (name + desc) |
| 事件 17 | 3 对 | title + desc + option |
| 脚本基础设施 | 0 | 无需本地化 |

**小计**：~52 对双语 key

### 14.2 事件文件改动追加

现有 events 1-16 追加 event_chain 调用的代码改动不需要额外本地化。

**总计**：~52 对双语 key

---

## 十五、文件操作清单

| 序号 | 操作 | 路径 | 说明 |
|------|------|------|------|
| 1 | MKDIR | `Starlike/common/buildings/` | 新目录 |
| 2 | NEW | `Starlike/common/buildings/sl_buildings.txt` | 3 派系帝国建筑 |
| 3 | MKDIR | `Starlike/common/pop_jobs/` | 新目录 |
| 4 | NEW | `Starlike/common/pop_jobs/sl_jobs.txt` | 3 岗位 |
| 5 | MKDIR | `Starlike/common/decisions/` | 新目录 |
| 6 | NEW | `Starlike/common/decisions/sl_decisions.txt` | 2 决议 |
| 7 | MKDIR | `Starlike/common/edicts/` | 新目录 |
| 8 | NEW | `Starlike/common/edicts/sl_edicts.txt` | 1 法令 |
| 9 | MKDIR | `Starlike/common/armies/` | 新目录 |
| 10 | NEW | `Starlike/common/armies/sl_armies.txt` | 1 陆军 |
| 11 | MKDIR | `Starlike/common/event_chains/` | 新目录 |
| 12 | NEW | `Starlike/common/event_chains/sl_event_chains.txt` | 3 事件链 |
| 13 | MKDIR | `Starlike/common/game_concepts/` | 新目录 |
| 14 | NEW | `Starlike/common/game_concepts/sl_concepts.txt` | 6 概念 |
| 15 | MKDIR | `Starlike/common/start_screen_messages/` | 新目录 |
| 16 | NEW | `Starlike/common/start_screen_messages/sl_start_screen.txt` | 1 开屏 |
| 17 | MKDIR | `Starlike/common/message_types/` | 新目录 |
| 18 | NEW | `Starlike/common/message_types/sl_messages.txt` | 2 消息类型 |
| 19 | MKDIR | `Starlike/common/scripted_triggers/` | 新目录 |
| 20 | NEW | `Starlike/common/scripted_triggers/sl_triggers.txt` | 公共触发器 |
| 21 | MKDIR | `Starlike/common/scripted_effects/` | 新目录 |
| 22 | NEW | `Starlike/common/scripted_effects/sl_effects.txt` | 公共效果 |
| 23 | EDIT | `Starlike/common/static_modifiers/sl_modifiers.txt` | +2 修正器 |
| 24 | EDIT | `Starlike/events/sl_homing_events.txt` | +event 17 + chain 调用 |
| 25 | EDIT | `Starlike/localisation/l_english/sl_l_english.yml` | ~52 key |
| 26 | EDIT | `Starlike/localisation/l_simp_chinese/sl_l_simp_chinese.yml` | ~52 key |

**总计**：11 新目录 + 11 新文件 + 4 编辑文件

---

## 十六、实现阶段拆分

### 阶段 1：脚本基础设施（独立可测）

- scripted_triggers + scripted_effects
- 验证：游戏加载无错误

### 阶段 2：建筑 + 岗位

- sl_buildings.txt（3 派系帝国建筑）+ sl_jobs.txt（3 岗位）+ 双语本地化
- 验证：首都建造菜单可见（仅对应 civic），帝国唯一限制生效，建成后岗位产出正确

### 阶段 3：决议 + 法令 + 修正器

- sl_decisions.txt + sl_edicts.txt + sl_modifiers.txt 追加 + 双语
- 验证：决议/法令在游戏中可见可执行

### 阶段 4：陆军

- sl_armies.txt + 双语
- 验证：招募界面可见，数值正确

### 阶段 5：事件链 + 消息类型 + 事件改动

- sl_event_chains.txt + sl_messages.txt + 事件文件追加 chain 调用 + event 17
- 验证：情报日志显示事件链进度

### 阶段 6：游戏概念 + 开屏消息

- sl_concepts.txt + sl_start_screen.txt + 双语
- 验证：百科中可查看，新游戏显示开屏

---

## 十七、关键不变式

1. **origin_homing gate**：所有新增系统必须受 origin_homing 限制
2. **不修改**：现有传统树、飞升、events 1-16 的核心逻辑（仅追加 chain 调用）
3. **UTF-8 BOM**：所有 yml 文件保持编码
4. **双语同步**：英中 yml 同步新增所有 key
5. **命名前缀**：所有 ID 使用 `sl_` 或 `building_sl_` 等项目前缀
6. **图标复用**：所有 GFX 引用使用原版路径，不依赖自定义 DDS

---

## 十八、风险与回退

| 风险 | 影响 | 缓解 |
|------|------|------|
| 建筑 category 不存在 | 建造菜单不显示 | 使用原版 category（unity, research）|
| 岗位 weight 不平衡 | pop 不愿就任 | 使用 @specialist_job_weight 原版变量 |
| 事件链 counter 溢出 | 日志显示异常 | abort_trigger 在完成时清理 |
| 原版 GFX 图标路径变更 | 图标不显示 | 使用最稳定的原版路径 |
| event_chain 与现有事件不联动 | 日志无进度 | 阶段 5 逐事件验证 |

**回退方案**：11 个新文件 + 4 个编辑文件，git revert 即可完整回退。

---

## 十九、完成定义（DoD）

- [ ] 3 个派系帝国建筑在首都建造菜单中显示（对应 civic 限定，帝国唯一）
- [ ] 3 个岗位在建筑建成后正常产出
- [ ] 2 个决议在满足条件时可执行（重建地球需完整触发链）
- [ ] 1 个法令在传统完成后可开启
- [ ] 1 个陆军在招募界面可见
- [ ] 3 条事件链在情报日志中追踪进度
- [ ] 6 个游戏概念 hover 时弹出工具提示
- [ ] 新游戏开屏显示归巢起源专属消息
- [ ] `error.log` 无相关报错
- [ ] 英中双语本地化完整
