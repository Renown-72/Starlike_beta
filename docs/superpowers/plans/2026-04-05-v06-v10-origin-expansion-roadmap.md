# 偌星：归巢计划 · 全版本更新路线图 v0.3 ~ v1.0

> **状态**: 规划中（v0.3 已有详细设计，待执行；v0.6-v1.0 本文档定义）
> **Owner**: Renown-72
> **最后更新**: 2026-04-05

---

## 一、顶层设计：五文明 · 全系统扩展

### 五个起源的定位与分工

| 版本 | 起源标识 | 文明名 | 主题 | 文化倾向 | 产业 | 舰队 | 状态 |
|------|---------|--------|------|---------|------|------|------|
| v0.3-v0.5 | `origin_homing` | 一体汉和 | 归巢派·Sol遗民·博物馆文明 | 自由开放·文化输出 | 金融/贸易/科技 | 巡洋舰/驱逐舰 | 70% |
| v0.6 | `origin_selene` | 赛琳娜之耀 | 光海泽生·生命循环·自然和谐 | 追求自然·生命价值 | 生物/基因/医疗 | 探索舰/护卫舰 | 新建 |
| v0.7 | `origin_holysight` | 圣瞳会 | 卡诺莎选民·灵能宗教·神迹宣扬 | 灵能研究·武力护教 | 教育/宗教/灵能科技 | 巡洋舰/潜伏舰 | 新建 |
| v0.8 | `origin_mountainpass` | 芒廷帕斯联合会 | 恶意收购·资本帝国·星际寡头 | 寡头开放·资本护私 | 金融/矿业/物流 | 商旅舰/护卫舰 | 新建 |
| v0.9 | `origin_shatumun` | 沙顿穆恩帝国 | 即见征服·军国帝国·文化优越 | 集体主义·优胜劣汰 | 军工业/机械重工 | 战列舰/航母/泰坦 | 新建 |
| v1.0 | — | 共享基础设施 | 跨文明交互·银河共同体 | — | — | — | 新建 |

### 每个起源的 7 层系统闭环

```
┌──────────────────────────────────────────────────────┐
│                   每个起源的 7 层系统                   │
├────┬─────────────────────┬──────────────────────────┤
│ 1  │ 起源定义              │ origin_xxx               │
│ 2  │ 派系国策 ×3          │ civic_xxx_main/sub1/sub2│
│ 3  │ 内阁议程 ×3          │ agenda_xxx_main/sub1/sub2│
│ 4  │ 传统树（7+ 节点）     │ tr_xxx_                 │
│ 5  │ 飞升天赋 ×2           │ ap_xxx_                 │
│ 6  │ 特殊建筑 ×4          │ bd_xxx_                 │
│ 7  │ 特色事件链（5+ 事件）  │ event_xxx.N             │
└────┴─────────────────────┴──────────────────────────┘
```

### 共享基础设施（5 个，v1.0 集中实现）

| # | 系统 | 文件路径 | 内容 |
|---|------|---------|------|
| A | 特殊资源（落日晶等） | `common/strategic_resources/Starlike_strategic_resources.txt` | 3-5 个文明专属资源 |
| B | 宣告/法令 | `common/edicts/Starlike_edicts.txt` | 5-8 个跨文明通用 edicts |
| C | 物种定义+肖像 | `common/species/` + `common/portrait_categories/` | 每个文明的物种 + 肖像 |
| D | 科技树扩展 | `common/technology/` | 每个文明的专属科技线 |
| E | 跨文明互动事件 | `events/Starlike_cross_origin_events.txt` | 5 个起源间的外交/冲突事件 |

---

## 二、版本 v0.3：Bug 修复 + 内阁议程

**状态**: 已有详细设计（`2026-03-31-v03-bugfix-agendas.md`），待执行。

### 任务清单

| # | 任务 | 文件 | 优先级 |
|---|------|------|--------|
| T1 | 创建异常定义触发 event_homing.2 | `common/anomalies/Starlike_anomalies.txt` (新建) | HIGH |
| T2 | 修复 event_homing.2 scope（planet_event→ship_event） | `events/Starlike_homing_events.txt` | HIGH |
| T3 | 修复 civic_huisu/civic_martis 重复修正器 | `common/governments/civics/Starlike_civics.txt` | MEDIUM |
| T4 | 清理 music 文件 + 同步版本号 | `music/StarlikeOST.txt` + `descriptor.mod` | LOW |
| T5 | 创建 3 个内阁议程 | `common/council_agendas/Starlike_council_agendas.txt` (新建) | MEDIUM |
| T6 | 添加英中双语本地化 | `localisation/*.yml` | MEDIUM |

---

## 三、版本 v0.4：传统树 + 飞升 + 派系事件链

**状态**: 已有详细设计（`2026-03-31-roadmap-v03-v05-design.md` 中定义）。

### 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/tradition_categories/Starlike_tradition_categories.txt` | 新建 | 归巢传统树类别定义 |
| T2 | `common/traditions/Starlike_traditions.txt` | 新建 | 归巢传统树（7 节点） |
| T3 | `common/ascension_perks/Starlike_ascension_perks.txt` | 新建 | 2 个归巢专属飞升（Alpenglow + Stellar Return） |
| T4 | `common/governments/councilors/Starlike_councilors.txt` | 修改 | 补充 v0.3 修复后的议程内容 |
| T5 | `events/Starlike_homing_events.txt` | 追加 | 事件 5-16（3 个派系链 + 晚期链） |
| T6 | `localisation/*.yml` | 追加 | ~58 对本地化键值 |

### 事件链概览

```
event_homing.5-.7   → Huisu（辉夙派）博物馆档案事件
event_homing.8-.10  → Martis（荧惑派）实验室发现事件
event_homing.11-.13 → Phoenix Plume（鸾羽派）战舰残骸事件
event_homing.14-.16 → 晚期汇聚链（Alpenglow 飞升触发）
```

---

## 四、版本 v0.5：世界构建

**状态**: 已有详细设计。

### 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/prescripted_countries/Starlike_prescripted_species.txt` | 新建 | 归巢文明物种预设 |
| T2 | `common/name_lists/Starlike_name_lists.txt` | 新建 | ~100-150 个命名条目 |
| T3 | `common/ship_designs/Starlike_ship_designs.txt` | 新建 | 归巢专属舰船设计 |
| T4 | `events/Starlike_homing_events.txt` | 追加 | 事件 17-20（跨派系 + 银河接触） |
| T5 | `localisation/*.yml` | 追加 | ~21 对本地化键值 |

---

## 五、版本 v0.6：起源 2 — 赛琳娜之耀（光海泽生）

**设计来源**: Excel 策划文档 — "赛琳娜之耀政体（光海泽生起源预设）"

### 5.1 系统设计

```
文化倾向：追求自然、和谐，宣扬生命本身的存在意义与价值，认为宇宙的运转本身即为一场生命循环
产业倾向：生物制药、尖端医疗、环境改造、基因工程
舰队倾向：探索舰、护卫舰、潜伏舰
```

### 5.2 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/governments/civics/Starlike_civics.txt` | 追加 | 起源 2 定义 + 3 个派系国策 |
| T2 | `common/governments/councilors/Starlike_councilors.txt` | 追加 | 3 个赛琳娜派系 councilor |
| T3 | `common/council_agendas/Starlike_council_agendas.txt` | 追加 | 3 个赛琳娜议程 |
| T4 | `common/tradition_categories/Starlike_tradition_categories.txt` | 追加 | 赛琳娜传统树类别 |
| T5 | `common/traditions/Starlike_traditions.txt` | 追加 | 赛琳娜传统树（7 节点） |
| T6 | `common/ascension_perks/Starlike_ascension_perks.txt` | 追加 | 2 个赛琳娜专属飞升 |
| T7 | `common/buildings/Starlike_buildings.txt` | 新建 | 4 个赛琳娜特殊建筑 |
| T8 | `common/solar_system_initializers/Starlike_initializers.txt` | 追加 | 赛琳娜开局星系 |
| T9 | `events/Starlike_selene_events.txt` | 新建 | 赛琳娜事件链（5+ 事件） |
| T10 | `localisation/*.yml` | 追加 | ~80 对本地化键值 |

### 5.3 派系设计

| 国策 ID | 名称（CN） | 主题 | 核心效果 |
|---------|-----------|------|---------|
| `civic_selene_luminex` | 光海派 | 生命循环·光能生命 | +生物研究 +光合收益 |
| `civic_selene_verde` | 泽生派 | 基因改造·生态工程 | +基因工程 +人口增长 |
| `civic_selene_cosmos` | 宇宙循环派 | 生命哲学·银河传播 | +外交吸引力 +难民吸引 |

### 5.4 特殊建筑设计

| 建筑 ID | 名称（CN） | 效果 |
|---------|-----------|------|
| `bd_selene_gene_lab` | 基因研究所 | +生物科研 +基因工程速度 |
| `bd_selene_bio_farm` | 生态农场 | +食物产出 +人口宜居度 |
| `bd_selene_healing_spire` | 治愈尖塔 | +医疗产出 +外交好感 |
| `bd_selene_life_sanctum` | 生命圣所 | +凝聚力 +人口幸福度 |

### 5.5 飞升天赋设计

| ID | 名称（CN） | 前置 | 效果 |
|----|-----------|------|------|
| `ap_selene_eternal_cycle` | 永恒循环 | 传统树完成 | +人口寿命 +幸福度 +基因改造上限 |
| `ap_selene_cosmic_life` | 宇宙生命 | 上述 + 基因科技 | 解锁"生命传播"宣告；+星系殖民速度 |

---

## 六、版本 v0.7：起源 3 — 圣瞳会（卡诺莎选民）

**设计来源**: Excel 策划文档 — "圣瞳会政体（卡诺莎选民起源预设）"

### 6.1 系统设计

```
文化倾向：潜心于对神迹、染山霞血脉的灵能研究并在银河中广泛宣扬其核心教义，对于蔑辱教义的势力会使用武力打击
产业倾向：教育业、宗教文化、灵能科技、艺术
舰队倾向：巡洋舰、潜伏舰
特殊：染山霞血脉飞升（归巢+圣瞳会通用）
```

### 6.2 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/governments/civics/Starlike_civics.txt` | 追加 | 起源 3 定义 + 3 个派系国策 |
| T2 | `common/governments/councilors/Starlike_councilors.txt` | 追加 | 3 个圣瞳会 councilor |
| T3 | `common/council_agendas/Starlike_council_agendas.txt` | 追加 | 3 个圣瞳会议程 |
| T4 | `common/tradition_categories/Starlike_tradition_categories.txt` | 追加 | 圣瞳会传统树类别 |
| T5 | `common/traditions/Starlike_traditions.txt` | 追加 | 圣瞳会传统树（7 节点） |
| T6 | `common/ascension_perks/Starlike_ascension_perks.txt` | 追加 | 2 个圣瞳会专属飞升 + 1 个跨起源"染山霞血脉"飞升 |
| T7 | `common/buildings/Starlike_buildings.txt` | 追加 | 4 个圣瞳会特殊建筑 |
| T8 | `common/solar_system_initializers/Starlike_initializers.txt` | 追加 | 圣瞳会开局星系 |
| T9 | `events/Starlike_holysight_events.txt` | 新建 | 圣瞳会事件链（5+ 事件） |
| T10 | `localisation/*.yml` | 追加 | ~80 对本地化键值 |

### 6.3 派系设计

| 国策 ID | 名称（CN） | 主题 | 核心效果 |
|---------|-----------|------|---------|
| `civic_holysight_divine` | 圣瞳派 | 灵能研究·神迹崇拜 | +灵能强度 +灵能研究速度 |
| `civic_holysight_blood` | 染血派 | 血脉传承·武力护教 | +军事强度 +军队伤害 |
| `civic_holysight_art` | 艺知派 | 宗教艺术·教义传播 | +凝聚力 +影响力产出 +文化吸引力 |

### 6.4 特殊建筑设计

| 建筑 ID | 名称（CN） | 效果 |
|---------|-----------|------|
| `bd_holysight_shrine` | 灵能圣殿 | +灵能强度 +灵能科研 |
| `bd_holysight_academy` | 神学院 | +教育产出 +领袖池 |
| `bd_holysight_tribunal` | 异端审判庭 | +军事强度 +审判速度 |
| `bd_holysight_cathedral` | 大教堂 | +凝聚力 +宗教文化吸引力 |

### 6.5 飞升天赋设计

| ID | 名称（CN） | 前置 | 效果 |
|----|-----------|------|------|
| `ap_holysight_psi_blood` | 染山霞血脉 | 传统树完成 | +灵能强度上限 +灵能科研 +解锁灵能领袖特质 |
| `ap_holysight_divine_judgment` | 神意审判 | 上述 + 灵能科技 III | +军事强度 +对异端惩罚；解锁"异端征讨"宣告 |

> **跨起源设计**: `ap_holysight_psi_blood` 对归巢起源也可见，但需完成归巢传统树 + 解锁对应科技，体现"染山霞血脉是跨文明的古老传承"这一设定。

---

## 七、版本 v0.8：起源 4 — 芒廷帕斯联合会（恶意收购）

**设计来源**: Excel 策划文档 — "芒廷帕斯联合会政体（恶意收购起源预设）"

### 7.1 系统设计

```
文化倾向：政治权力高度寡头化，对移民接纳持较为开放的心态，鼓励多劳多得，注重保护资本私有
产业倾向：金融、行星矿业、地质勘探、星际物流、大型建筑、生物机械
舰队倾向：商旅舰、护卫舰
```

### 7.2 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/governments/civics/Starlike_civics.txt` | 追加 | 起源 4 定义 + 3 个派系国策 |
| T2 | `common/governments/councilors/Starlike_councilors.txt` | 追加 | 3 个芒廷帕斯 councilor |
| T3 | `common/council_agendas/Starlike_council_agendas.txt` | 追加 | 3 个芒廷帕斯议程 |
| T4 | `common/tradition_categories/Starlike_tradition_categories.txt` | 追加 | 芒廷帕斯传统树类别 |
| T5 | `common/traditions/Starlike_traditions.txt` | 追加 | 芒廷帕斯传统树（7 节点） |
| T6 | `common/ascension_perks/Starlike_ascension_perks.txt` | 追加 | 2 个芒廷帕斯专属飞升 |
| T7 | `common/buildings/Starlike_buildings.txt` | 追加 | 4 个芒廷帕斯特殊建筑 |
| T8 | `common/solar_system_initializers/Starlike_initializers.txt` | 追加 | 芒廷帕斯开局星系 |
| T9 | `events/Starlike_mountainpass_events.txt` | 新建 | 芒廷帕斯事件链（5+ 事件） |
| T10 | `localisation/*.yml` | 追加 | ~80 对本地化键值 |

### 7.3 派系设计

| 国策 ID | 名称（CN） | 主题 | 核心效果 |
|---------|-----------|------|---------|
| `civic_mountainpass_finance` | 金融派 | 资本运作·星际投资 | +能量币产出 +贸易吸引力 |
| `civic_mountainpass_mining` | 矿业派 | 行星开采·资源掠夺 | +矿物产出 +工程学研究 |
| `civic_mountainpass_logistics` | 物流派 | 星际运输·商业网络 | +贸易值 +舰船维护折扣 |

### 7.4 特殊建筑设计

| 建筑 ID | 名称（CN） | 效果 |
|---------|-----------|------|
| `bd_mountainpass_exchange` | 星际交易所 | +能量币 +贸易吸引力 |
| `bd_mountainpass_mine` | 深层矿场 | +矿物 +工程学 |
| `bd_mountainpass_hub` | 星际物流枢纽 | +舰船建造速度 +物流产出 |
| `bd_mountainpass_bio_mech` | 生物机械中心 | +研究速度 +人口产出 |

### 7.5 飞升天赋设计

| ID | 名称（CN） | 前置 | 效果 |
|----|-----------|------|------|
| `ap_mountainpass_corporate_empire` | 企业帝国 | 传统树完成 | +贸易值加成 +企业型外交 +商业协议数上限 |
| `ap_mountainpass_hostile_takeover` | 恶意收购 | 上述 + 相应科技 | 解锁"收购宣告"；对其他帝国 +贸易限制能力 |

---

## 八、版本 v0.9：起源 5 — 沙顿穆恩帝国（即见征服）

**设计来源**: Excel 策划文档 — "沙顿穆恩帝国政体（即见征服起源预设）"

### 8.1 系统设计

```
文化倾向：高程度的本土文化优越感，强调集体主义和文艺管控，对外崇尚优胜劣汰的丛林法则，对内极力完善社会保障
产业倾向：军工业、纪实文学及影视、建筑业、机械重工、雕塑、人像绘画
舰队倾向：战列舰、航空母舰、泰坦
```

### 8.2 文件清单

| # | 文件 | 操作 | 内容 |
|---|------|------|------|
| T1 | `common/governments/civics/Starlike_civics.txt` | 追加 | 起源 5 定义 + 3 个派系国策 |
| T2 | `common/governments/councilors/Starlike_councilors.txt` | 追加 | 3 个沙顿穆恩 councilor |
| T3 | `common/council_agendas/Starlike_council_agendas.txt` | 追加 | 3 个沙顿穆恩议程 |
| T4 | `common/tradition_categories/Starlike_tradition_categories.txt` | 追加 | 沙顿穆恩传统树类别 |
| T5 | `common/traditions/Starlike_traditions.txt` | 追加 | 沙顿穆恩传统树（7 节点） |
| T6 | `common/ascension_perks/Starlike_ascension_perks.txt` | 追加 | 2 个沙顿穆恩专属飞升 |
| T7 | `common/buildings/Starlike_buildings.txt` | 追加 | 4 个沙顿穆恩特殊建筑 |
| T8 | `common/solar_system_initializers/Starlike_initializers.txt` | 追加 | 沙顿穆恩开局星系 |
| T9 | `events/Starlike_shatumun_events.txt` | 新建 | 沙顿穆恩事件链（5+ 事件） |
| T10 | `localisation/*.yml` | 追加 | ~80 对本地化键值 |

### 8.3 派系设计

| 国策 ID | 名称（CN） | 主题 | 核心效果 |
|---------|-----------|------|---------|
| `civic_shatumun_war` | 征服派 | 军事帝国·武力扩张 | +舰船伤害 +军队规模 +战争倾向 |
| `civic_shatumun_culture` | 文治派 | 文化管控·集体认同 | +凝聚力 +帝国尺寸减免 +社会稳定 |
| `civic_shatumun_industry` | 军工派 | 机械重工·军工生产 | +舰船建造速度 +装甲强度 +工程学 |

### 8.4 特殊建筑设计

| 建筑 ID | 名称（CN） | 效果 |
|---------|-----------|------|
| `bd_shatumun_arsenal` | 皇家兵工厂 | +舰船建造速度 +武器伤害 |
| `bd_shatumun_monument` | 帝国纪念碑 | +凝聚力 +文化影响力 |
| `bd_shatumun_artillery` | 重工综合体 | +舰船伤害 +矿物维护折扣 |
| `bd_shatumun_citadel` | 文明堡垒 | +防御能力 +军队规模 +居民幸福度 |

### 8.5 飞升天赋设计

| ID | 名称（CN） | 前置 | 效果 |
|----|-----------|------|------|
| `ap_shatumun_imperial_legacy` | 帝国荣光 | 传统树完成 | +舰船伤害 +军队规模 +巨型结构容量 |
| `ap_shatumun_conquest` | 即见征服 | 上述 + 相应科技 | 解锁"征服宣告"；+战争吸引力 +泰坦建造 |

---

## 九、版本 v1.0：共享基础设施 + 跨文明交互

### 9.1 特殊资源系统

| 资源 ID | 名称（CN） | 来源 | 用途 |
|---------|-----------|------|------|
| `sr_sunset_crystal` | 落日晶 | 归巢起源专属 | 增强异常研究/考古 |
| `sr_psi_essence` | 灵能精华 | 圣瞳会起源专属 | 灵能强度增益 |
| `sr_bio_core` | 生物核心 | 赛琳娜起源专属 | 基因工程加速 |
| `sr_capital_token` | 资本令牌 | 芒廷帕斯起源专属 | 贸易值乘区 |
| `sr_imperial_stamp` | 帝国印记 | 沙顿穆恩起源专属 | 军事强度增益 |

**文件**: `common/strategic_resources/Starlike_strategic_resources.txt`（新建）

### 9.2 宣告/法令系统

**文件**: `common/edicts/Starlike_edicts.txt`（新建）

| 宣告 ID | 名称（CN） | 归属 | 效果 |
|---------|-----------|------|------|
| `edict_homing_return` | 归巢令 | 归巢 | +凝聚力 +影响力；需阿尔卑光飞升 |
| `edict_selene_cycle` | 生命循环 | 赛琳娜 | +人口增长；需永恒循环飞升 |
| `edict_holysight_judgment` | 异端征讨 | 圣瞳会 | +军事强度；需神意审判飞升 |
| `edict_mountainpass_takeover` | 收购宣告 | 芒廷帕斯 | 商业限制；需恶意收购飞升 |
| `edict_shatumun_conquest` | 征服宣告 | 沙顿穆恩 | 战争吸引力；需即见征服飞升 |
| `edict_universal_solidarity` | 银河团结 | 通用 | 跨文明外交；需完成多传统树 |

### 9.3 物种+肖像系统

**文件**:
- `common/species/Starlike_species.txt`（新建）
- `common/portrait_categories/Starlike_portrait_categories.txt`（新建）
- `gfx/portraits/Starlike_portraits/`（新建，参考 `代码参考（必看）/自建物种代码/`）

| 物种 ID | 名称（CN） | 起源 | 肖像类型 |
|---------|-----------|------|---------|
| `species_homing` | 归巢人类 | origin_homing | 类人（复用 vanilla HUMAN） |
| `species_selene` | 赛琳娜光灵 | origin_selene | 光能生物（特效） |
| `species_holysight` | 圣瞳信徒 | origin_holysight | 类人（灵能特效） |
| `species_mountainpass` | 芒廷帕斯人 | origin_mountainpass | 类人（机械增强） |
| `species_shatumun` | 沙顿穆恩人 | origin_shatumun | 类人（军事强化） |

### 9.4 科技树扩展

**文件**: `common/technology/Starlike_technologies.txt`（新建）

| 科技线 | 节点数 | 归属 |
|--------|--------|------|
| 归巢科技线 | 5 | origin_homing |
| 赛琳娜生物线 | 5 | origin_selene |
| 圣瞳灵能线 | 5 | origin_holysight |
| 芒廷帕斯资本线 | 5 | origin_mountainpass |
| 沙顿穆恩军工线 | 5 | origin_shatumun |

### 9.5 跨文明互动事件

**文件**: `events/Starlike_cross_origin_events.txt`（新建）

| 事件 | 触发条件 | 叙事 | 效果 |
|------|---------|------|------|
| event_cross.1 | 两个不同偌星起源帝国首次接触 | "故人相逢"：同为文明遗民的不同命运 | +/— 外交态度 + 共同研究机会 |
| event_cross.2 | 归巢 + 圣瞳会同时存在 | "血脉共鸣"：染山霞血脉的跨文明传承 | 解锁特殊合作选项 |
| event_cross.3 | 任意三个偌星起源帝国存在 | "银河文明圈"：偌星文明圈建立 | 全偌星帝国 +凝聚力 +影响力 |
| event_cross.4 | 芒廷帕斯 + 沙顿穆恩 + 任意其他 | "资本 vs 武力"：经济联盟与军事联盟的对立 | 事件链分支：合作或冲突 |
| event_cross.5 | 四个以上偌星起源帝国 | "银河委员会"：偌星文明联邦可能性 | 最终叙事选择：联盟/竞争/统一 |

---

## 十、版本依赖图

```
v0.3 (Bug Fix + Agendas)
  └─ v0.4 (Traditions + Ascension + Events)
        ├─ v0.5 (Species + Names + Ships)
        └─ v0.6 (Origin 2: Selene)
              └─ v0.7 (Origin 3: HolySight)
                    └─ v0.8 (Origin 4: MountainPass)
                          └─ v0.9 (Origin 5: Shatumun)
                                └─ v1.0 (Shared Infra + Cross-Origin)
```

---

## 十一、工作量估算

| 版本 | 新文件数 | 新建目录数 | 本地化键值对 | 事件数 | 建筑数 | 飞升数 | 传统节点 |
|------|---------|----------|------------|-------|-------|-------|---------|
| v0.3 | 2 | 2 | ~10 | — | — | — | — |
| v0.4 | 4 | 2 | ~58 | +12 | — | +2 | +7 |
| v0.5 | 3 | 1 | ~21 | +4 | — | — | — |
| v0.6 | 4 | 1 | ~80 | +5 | +4 | +2 | +7 |
| v0.7 | 4 | 1 | ~80 | +5 | +4 | +3* | +7 |
| v0.8 | 4 | 1 | ~80 | +5 | +4 | +2 | +7 |
| v0.9 | 4 | 1 | ~80 | +5 | +4 | +2 | +7 |
| v1.0 | 5 | 3 | ~60 | +5 | — | — | — |
| **合计** | **30** | **12** | **~389** | **+46** | **+16** | **+11** | **+35** |
