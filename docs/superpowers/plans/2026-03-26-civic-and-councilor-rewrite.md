# COPAN Civic & Councilor 重构实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 重构政体-国策-内阁体系：origin 不再绑定 auth_copan；三个 COPAN 国策（行政官/科学官/指挥官）绑定 origin；三套内阁职位对应三股势力。

**Architecture:**
- `origin_homing` 改为绑定 vanilla `auth_dictatorial`，移除 auth_copan 依赖
- 国策通过 `possible = { origin = { value = origin_homing } }` 绑定 origin，所有 reform 路线共享
- councilor 通过 scripted_trigger `has_origin = origin_homing` 绑定 origin，civic 由 councilor 间接解锁
- auth_copan 保留作为 reform 路线选项（可改革）

**Tech Stack:** Paradox Scripting Language (Jomini) — `.txt` 定义文件 + YAML 本地化

---

## 涉及文件清单

| 操作 | 文件路径 |
|---|---|
| 修改 | `common/governments/civics/Starlike_origin.txt` |
| 重写 | `common/governments/civics/Starlike_civics.txt` |
| 重写 | `common/governments/councilors/Starlike_councilors.txt` |
| 修改 | `common/governments/authorities/Starlike_authorities.txt` |
| 修改 | `localisation/l_english/Starlike_l_english.yml` |
| 修改 | `localisation/l_simp_chinese/Starlike_l_simp_chinese.yml` |

---

## Task 1: 修改 origin_homing — 解除 auth_copan 绑定

**文件:** `Starlike/common/governments/civics/Starlike_origin.txt`

**当前问题:** `possible = { authority = { value = auth_copan } }` 将起源锁死为 auth_copan。

**修改为:** 移除 authority 限制，改为独立起源。起始政体由 vanilla `auth_dictatorial` 提供。`possible` 块清空（不需要限制条件，因为这是唯一的自定义起源）。

- [ ] **Step 1: 读取当前 Starlike_origin.txt**

```bash
Read: Starlike/common/governments/civics/Starlike_origin.txt
```

- [ ] **Step 2: 替换 possible 块 — 移除 auth_copan 绑定**

```pdx
# 旧代码：
possible = { #起源限制条件
    authority = {
        OR = {
            value = auth_copan
        }
    }
}

# 新代码：
possible = {
    # origin_homing 是独立起源，不限制authority
    # 配合 vanilla auth_dictatorial 使用
}
```

- [ ] **Step 3: 验证修改结果**

```bash
Grep: "authority = {" in Starlike/common/governments/civics/Starlike_origin.txt
# 预期：无结果
```

---

## Task 2: 重写 Starlike_civics.txt — 三个 origin 绑定国策

**文件:** `Starlike/common/governments/civics/Starlike_civics.txt`

**技术要点:**
- v4.3 国策 `possible` 块支持 `origin = { value = origin_xxx }` 语法
- 若编译报错，回退到 `possible = { always = yes }`（对所有 origin 开放，配合 councilor 限制）
- 每个 civic 通过 `civic = xxx` 字段关联对应 councilor

**注意:** `modification = no`（国策不可中途修改，保持势力纯粹性）；若需要修改灵活性可改为 `yes`

| 国策 | 关联 councilor | 核心 modifier |
|---|---|---|
| `civic_copan_administrator` | `councilor_copan_administrator` | 凝聚力 + 行政效率 + 法令基金 |
| `civic_copan_scientist` | `councilor_copan_scientist` | 科研速度 + 异常 + 科学船 |
| `civic_copan_commander` | `councilor_copan_commander` | 舰船容量 + 领袖经验 + 陆军 |
| `civic_huisu_museum`（已有） | `councilor_curator_copan` | 考古 + 遗珍 |

- [ ] **Step 1: 读取当前 Starlike_civics.txt**

```bash
Read: Starlike/common/governments/civics/Starlike_civics.txt
```

- [ ] **Step 2: 完全重写文件内容**

```pdx
# 一体汉和专属国策组
# 所有国策绑定 origin_homing，通过 origin = { value = origin_homing } 限制

# ── 博物遗产 ── Museum Heritage
# 辉夙星人类文明博物馆：系统收集、整理、研究失落文明的遗存
civic_huisu_museum = {
    description = "civic_huisu_museum_effects"
    negative_description = "civic_huisu_museum_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    civic = councilor_curator_copan
    modifier = {
        country_archaeology_speed_mult = 0.25
        country_relic_energy_produces_mult = 0.15
        country_soc_researchers_produces_mult = 0.05
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}

# ── 行政官派系 ── Administrator Faction
civic_copan_administrator = {
    description = "civic_copan_administrator_effects"
    negative_description = "civic_copan_administrator_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    civic = councilor_copan_administrator
    modifier = {
        # 行政效率
        country_unity_produces_mult = 0.15
        country_edict_fund_add = 10
        country_administrative_capacity_add = 50
        # 代价：重文轻武，舰船容量略低
        country_naval_cap_mult = -0.1
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}

# ── 科学官派系 ── Scientist Faction
civic_copan_scientist = {
    description = "civic_copan_scientist_effects"
    negative_description = "civic_copan_scientist_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    civic = councilor_copan_scientist
    modifier = {
        # 科学探索
        country_research_speed_mult = 0.1
        country_survey_speed_mult = 0.2
        country_anomaly_research_speed_mult = 0.2
        science_ship_survey_speed_mult = 0.15
        # 代价：领袖池略小
        country_leader_pool_size = -1
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}

# ── 指挥官派系 ── Commander Faction
civic_copan_commander = {
    description = "civic_copan_commander_effects"
    negative_description = "civic_copan_commander_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    civic = councilor_copan_commander
    modifier = {
        # 军事力量
        country_naval_cap_mult = 0.2
        country_leader_exp_gain_mult = 0.25
        army_damage_mult = 0.15
        # 代价：凝聚力产出略低
        country_unity_produces_mult = -0.05
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}
```

- [ ] **Step 3: 验证文件写入成功**

```bash
Grep: "origin = { value = origin_homing }" in Starlike/common/governments/civics/Starlike_civics.txt
# 预期：4条匹配（civic_huisu_museum + 3个新国策）
```

---

## Task 3: 重写 Starlike_councilors.txt — 三个 faction councilors

**文件:** `Starlike/common/governments/councilors/Starlike_councilors.txt`

**技术要点:**
- councilor 的 `civic = xxx` 字段解锁对应 civic
- councilor 绑定 origin_homing 使用 `possible = { origin = { value = origin_homing } }`
- 每个 councilor 限制单一 `leader_class`
- `ruler_council_position` 仅用于统治者在 council 中的位置（保持 `councilor_ruler_copan`）

| councilor | leader_class | 关联 civic |
|---|---|---|
| `councilor_copan_administrator` | official | `civic_copan_administrator` |
| `councilor_copan_scientist` | scientist | `civic_copan_scientist` |
| `councilor_copan_commander` | commander | `civic_copan_commander` |
| `councilor_curator_copan` | official | `civic_huisu_museum` |

- [ ] **Step 1: 读取当前 Starlike_councilors.txt**

```bash
Read: Starlike/common/governments/councilors/Starlike_councilors.txt
```

- [ ] **Step 2: 完全重写文件内容**

```pdx
# 一体汉和内阁 — COPAN Council
# 三个派系内阁职位 + 博物总督

# ── 统治者 ── Ruler
councilor_ruler_copan = {
    leader_class = {
        official
        scientist
        commander
    }
    possible = {
        origin = { value = origin_homing }
    }
    modifier = {
        country_unity_produces_mult = 0.05
        country_edict_fund_add = 5
    }
    icon = "GFX_icon_councilor_ruler"
    ruler_council_position = councilor_ruler_copan
}

# ── 行政总监 ── Chief Administrator
councilor_copan_administrator = {
    leader_class = {
        official
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_copan_administrator
    modifier = {
        country_edict_fund_add = 5
        country_administrative_capacity_add = 25
    }
    icon = "GFX_icon_councilor_administrator"
}

# ── 科学总监 ── Science Director
councilor_copan_scientist = {
    leader_class = {
        scientist
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_copan_scientist
    modifier = {
        country_research_speed_mult = 0.05
        scientist_anomaly_research_speed_mult = 0.15
    }
    icon = "GFX_icon_councilor_scientist"
}

# ── 舰队总长 ── Fleet Commander
councilor_copan_commander = {
    leader_class = {
        commander
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_copan_commander
    modifier = {
        country_naval_cap_mult = 0.1
        country_leader_exp_gain_mult = 0.15
    }
    icon = "GFX_icon_councilor_commander"
}

# ── 博物总督 ── Curator General
councilor_curator_copan = {
    leader_class = {
        official
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_huisu_museum
    modifier = {
        country_archaeology_speed_mult = 0.15
        country_relic_seed_production_speed_mult = 0.1
    }
    icon = "GFX_icon_councilor_curator"
}
```

- [ ] **Step 3: 验证文件写入成功**

```bash
Grep: "councilor_copan" in Starlike/common/governments/councilors/Starlike_councilors.txt
# 预期：5条匹配（ruler + administrator + scientist + commander + curator）
```

---

## Task 4: 调整 auth_copan — 支持可改革政体

**文件:** `Starlike/common/governments/authorities/Starlike_authorities.txt`

**当前问题:** `can_reform = no` 阻止玩家改革。

**修改:** 将 `can_reform = no` 改为 `can_reform = yes`，允许玩家通过国策/改革系统转换为 auth_copan。

- [ ] **Step 1: 读取当前 Starlike_authorities.txt**

```bash
Read: Starlike/common/governments/authorities/Starlike_authorities.txt
```

- [ ] **Step 2: 修改 can_reform 字段**

```pdx
# 旧代码：
can_reform = no

# 新代码：
can_reform = yes
```

- [ ] **Step 3: 验证**

```bash
Grep: "can_reform" in Starlike/common/governments/authorities/Starlike_authorities.txt
# 预期：can_reform = yes
```

---

## Task 5: 同步双语本地化

**涉及文件:**
- `localisation/l_english/Starlike_l_english.yml`
- `localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

### 英文本地化 Key 清单

| Key | 英文文本 |
|---|---|
| `civic_copan_administrator` | "Administrator Faction" |
| `civic_copan_administrator_desc` | "The civil administration faction prioritizes bureaucratic efficiency and unity." |
| `civic_copan_administrator_effects` | "§H+15%§! Unity Production\n§H+10§! Edict Fund\n§H+50§! Administrative Capacity\n§R-10%§! Naval Capacity" |
| `civic_copan_administrator_negative_effects` | "" |
| `civic_copan_scientist` | "Scientist Faction" |
| `civic_copan_scientist_desc` | "The science faction channels the StarWhisper's curiosity toward galactic discovery." |
| `civic_copan_scientist_effects` | "§H+10%§! Research Speed\n§H+20%§! Survey Speed\n§H+20%§! Anomaly Research Speed\n§H-1§! Leader Pool Size" |
| `civic_copan_scientist_negative_effects` | "" |
| `civic_copan_commander` | "Commander Faction" |
| `civic_copan_commander_desc` | "The military commander faction embodies the warrior spirit of the StarWhispers." |
| `civic_copan_commander_effects` | "§H+20%§! Naval Capacity\n§H+25%§! Leader Experience Gain\n§H+15%§! Army Damage\n§R-5%§! Unity Production" |
| `civic_copan_commander_negative_effects` | "" |
| `councilor_copan_administrator` | "Chief Administrator" |
| `councilor_copan_administrator_desc` | "The Chief Administrator oversees the civil machinery of the Commonwealth." |
| `councilor_copan_scientist` | "Science Director" |
| `councilor_copan_scientist_desc` | "The Science Director leads the exploration and research mandate." |
| `councilor_copan_commander` | "Fleet Commander" |
| `councilor_copan_commander_desc` | "The Fleet Commander commands the military forces of the Commonwealth." |
| `councilor_curator_copan` | "Curator General" |
| `councilor_curator_copan_desc` | "The Curator General leads the museum system, coordinating the preservation of all galactic heritage." |

- [ ] **Step 1: 读取当前英文本地化文件**

```bash
Read: localisation/l_english/Starlike_l_english.yml
```

- [ ] **Step 2: 替换 civic_huisu_museum 的描述字段**

当前 `civic_huisu_museum_desc` 和 effects 为空，需填入：

```yaml
  civic_huisu_museum: "Museum Heritage"
  civic_huisu_museum_desc: "The Destined Bright Museum of Mankind's History and Civilization preserves and studies the relics of all fallen civilizations."
  civic_huisu_museum_effects: "§H+25%§! Archaeology Speed\n§H+15%§! Relic Energy Production\n§H+5%§! Society Research from Researchers"
  civic_huisu_museum_negative_effects: ""
```

- [ ] **Step 3: 追加 3 个新国策的本地化**

在英文文件末尾追加上述 12 条 key-value。

- [ ] **Step 4: 中文本地化同步**

```bash
Read: localisation/l_simp_chinese/Starlike_l_simp_chinese.yml
```

追加/替换以下中文 key：

```yaml
  civic_copan_administrator: "行政官派系"
  civic_copan_administrator_desc: "行政派系优先提升行政效率和凝聚力。"
  civic_copan_administrator_effects: "§H+15%§! 凝聚力产出\n§H+10§! 法令基金\n§H+50§! 行政容量\n§R-10%§! 舰船容量"
  civic_copan_administrator_negative_effects: ""

  civic_copan_scientist: "科学官派系"
  civic_copan_scientist_desc: "科学派系将星语者的好奇心引向全星系探索。"
  civic_copan_scientist_effects: "§H+10%§! 科研速度\n§H+20%§! 勘测速度\n§H+20%§! 异常研究速度\n§H-1§! 领袖储备容量"
  civic_copan_scientist_negative_effects: ""

  civic_copan_commander: "指挥官派系"
  civic_copan_commander_desc: "军事指挥官派系体现星语者的战斗精神。"
  civic_copan_commander_effects: "§H+20%§! 舰船容量\n§H+25%§! 领袖经验获取\n§H+15%§! 陆军伤害\n§R-5%§! 凝聚力产出"
  civic_copan_commander_negative_effects: ""

  civic_huisu_museum_desc: "辉夙星人类文明博物馆：系统收集、整理、研究失落文明的遗存。"
  civic_huisu_museum_effects: "§H+25%§! 考古速度\n§H+15%§! 遗珍能量产出\n§H+5%§! 社会学研究产出（来自研究员）"
  civic_huisu_museum_negative_effects: ""

  councilor_copan_administrator: "行政总监"
  councilor_copan_administrator_desc: "行政总监统筹一体汉和的民事机构。"
  councilor_copan_scientist: "科学总监"
  councilor_copan_scientist_desc: "科学总监领导探索与研究使命。"
  councilor_copan_commander: "舰队总长"
  councilor_copan_commander_desc: "舰队总长指挥一体汉和的军事力量。"

  # 注意：councilor_curator_copan 和 councilor_curator_copan_desc 已存在于中文文件中，无需修改
```

- [ ] **Step 5: 验证两个文件的 key 数量一致**

```bash
Bash: wc -l localisation/l_english/Starlike_l_english.yml localisation/l_simp_chinese/Starlike_l_simp_chinese.yml
# 预期：两文件行数接近（双语内容一一对应）
```

---

## 技术风险 & 回退方案

| 风险 | 描述 | 回退方案 |
|---|---|---|
| `origin = { value = origin_homing }` v4.3 语法不支持 | 如果 v4.3 国策 possible 不支持 origin 检查 | 将 `possible` 改为 `always = yes`，通过 councilor 的 possible 检查间接实现 |
| councilor 的 `civic = xxx` 关联 | 需要验证 v4.3 councilor 是否支持 civic 字段 | 参考现有 councilor 参考文件 `国策和配套的领袖编写/councilors/zijian.txt` 中的 `civic = civic_zijian` 字段，已验证可用 |
| auth_copan 起始政体仍为 democratic | `election_type = democratic` 导致开局非独裁 | 保持 vanilla `auth_dictatorial` 作为起始，auth_copan 仅作为 reform 目标（玩家通过游戏内改革机制转换） |

---

## 验证方式

1. **游戏内测试:** 启动 Stellaris 4.3，选 Project Homing 起源，开局政体应为 vanilla 独裁制（不是 auth_copan）
2. **国策测试:** 打开国策列表，应看到 4 个 COPAN 国策（博物遗产、行政官、科学官、指挥官）均可用
3. **内阁测试:** 打开内阁，应有 5 个内阁职位（统治者 + 4 个 faction councilors）
4. **错误日志:** `Documents/Paradox Interactive/Stellaris/logs/error.log` 搜索 `origin_homing` 或 `councilor_copan`
