# 派系命名重构实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: use superpowers:subagent-driven-development 执行本计划。Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 基于新叙事背景，重构三个派系国策及对应 councilor 的命名和描述。辉夙派（民主路线）、荧惑派（独裁路线）、鸾羽派（帝制路线），三个国策 key 统一使用 pinyin 命名。

**Architecture:**
- `civic_huisu_museum` 与 `civic_copan_administrator` 合并为 `civic_huixu`（辉夙派，民主制路线）
- `civic_copan_scientist` 重命名为 `civic_yinghuo`（荧惑派，独裁制路线）
- `civic_copan_commander` 重命名为 `civic_luanyu`（鸾羽派，帝制路线）
- councilors 文件同步重命名，councilor_curator_copan + councilor_copan_administrator 合并为 councilor_huixu
- 本地化 key 全部更新

**Tech Stack:** Paradox Scripting Language (Jomini) — `.txt` 定义文件 + YAML 本地化

---

## 叙事背景（开发参考）

### 辉夙派（civic_huixu）— 民主制路线
归巢计划的主体船员为辉夙星人类文明博物馆的成员。他们携带着地球文明的记忆与遗珍，在漫长的流亡中以博物精神维系文明认同。辉夙派掌权时，优先传承与治理效率。

### 荧惑派（civic_yinghuo）— 独裁制路线
归巢计划执行期间，地球已被星尘同化为恒星。船团被迫在火星重建文明，在极端恶劣的环境下，科学家群体以集体智慧带领文明存续。现任执政官即出身科学官，其支持者自称荧惑派——取意火星上仅存的光亮。

### 鸾羽派（civic_luanyu）— 帝制路线
鸾羽号为监视归巢计划而派遣的武装舰船武官组成。他们原是帝国政府安插的监督力量，却因意外与船团一同来到了群星位面。鸾羽派主张以武力保障文明安全，崇尚军事秩序。

---

## 涉及文件清单

| 操作 | 文件路径 |
|---|---|
| 重写 | `Starlike/common/governments/civics/Starlike_civics.txt` |
| 重写 | `Starlike/common/governments/councilors/Starlike_councilors.txt` |
| 修改 | `Starlike/localisation/l_english/Starlike_l_english.yml` |
| 修改 | `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml` |

---

## Task 1: 重写 Starlike_civics.txt — 三个派系国策

**文件:** `Starlike/common/governments/civics/Starlike_civics.txt`

**变更说明:**
- 删除 `civic_huisu_museum` 和 `civic_copan_administrator`，合并为 `civic_huixu`
- `civic_copan_scientist` → `civic_yinghuo`
- `civic_copan_commander` → `civic_luanyu`

| 国策 ID | 路线 | 核心 modifier |
|---|---|---|
| `civic_huixu` | 民主制 | country_archaeology_speed_mult=0.25, country_relic_energy_produces_mult=0.15, country_unity_produces_mult=0.15, country_edict_fund_add=10, country_naval_cap_mult=-0.1 |
| `civic_yinghuo` | 独裁制 | country_research_speed_mult=0.1, country_survey_speed_mult=0.2, country_anomaly_research_speed_mult=0.2, science_ship_survey_speed_mult=0.15, country_leader_pool_size=-1 |
| `civic_luanyu` | 帝制 | country_naval_cap_mult=0.2, country_leader_exp_gain_mult=0.25, army_damage_mult=0.15, country_unity_produces_mult=-0.05 |

**modifier 说明:**
- 辉夙派合并了考古加成（博物馆职能）和行政加成（治理效率），代价为舰船容量
- 荧惑派保留科研探索加成，代价为领袖池
- 鸾羽派保留军事加成，代价为凝聚力

- [ ] **Step 1: 读取当前 Starlike_civics.txt**

```bash
Read: Starlike/common/governments/civics/Starlike_civics.txt
```

- [ ] **Step 2: 完全重写文件内容**

```pdx
# 一体汉和专属国策组
# 所有国策绑定 origin_homing，通过 origin = { value = origin_homing } 限制

# ── 辉夙派 ── Huixu Faction（民主制路线）
# 归巢计划主体为博物馆成员，以博物精神维系文明认同
civic_huixu = {
    description = "civic_huixu_effects"
    negative_description = "civic_huixu_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    modifier = {
        # 博物传承：考古与遗珍收集
        country_archaeology_speed_mult = 0.25
        country_relic_energy_produces_mult = 0.15
        # 治理效率：博物馆成员的行政传统
        country_unity_produces_mult = 0.15
        country_edict_fund_add = 10
        # 代价：重文轻武，舰船容量略低
        country_naval_cap_mult = -0.1
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}

# ── 荧惑派 ── Yinghuo Faction（独裁制路线）
# 荧惑派：出身于火星重建时期的科学官群体，以集体智慧带领文明存续
civic_yinghuo = {
    description = "civic_yinghuo_effects"
    negative_description = "civic_yinghuo_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    modifier = {
        # 科学探索：荧惑派的科研优势
        country_research_speed_mult = 0.1
        country_survey_speed_mult = 0.2
        country_anomaly_research_speed_mult = 0.2
        science_ship_survey_speed_mult = 0.15
        # 代价：领袖池略小（科学治国，人才集中）
        country_leader_pool_size = -1
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}

# ── 鸾羽派 ── Luanyu Faction（帝制路线）
# 鸾羽派：鸾羽号武官群体，以武力保障文明安全，崇尚军事秩序
civic_luanyu = {
    description = "civic_luanyu_effects"
    negative_description = "civic_luanyu_negative_effects"
    modification = no
    possible = {
        origin = { value = origin_homing }
    }
    modifier = {
        # 军事力量：鸾羽派的武装传统
        country_naval_cap_mult = 0.2
        country_leader_exp_gain_mult = 0.25
        army_damage_mult = 0.15
        # 代价：凝聚力产出略低（武力压制异议）
        country_unity_produces_mult = -0.05
    }
    random_weight = { base = 0 }
    ai_weight = { base = 0 }
}
```

- [ ] **Step 3: 验证文件写入成功**

```bash
Grep: "origin = { value = origin_homing }" in Starlike/common/governments/civics/Starlike_civics.txt
# 预期：3条匹配（civic_huixu + civic_yinghuo + civic_luanyu）
Grep: "civic_huixu" in Starlike/common/governments/civics/Starlike_civics.txt
# 预期：1条
Grep: "civic_yinghuo" in Starlike/common/governments/civics/Starlike_civics.txt
# 预期：1条
Grep: "civic_luanyu" in Starlike/common/governments/civics/Starlike_civics.txt
# 预期：1条
```

---

## Task 2: 重写 Starlike_councilors.txt — 三个派系 councilor

**文件:** `Starlike/common/governments/councilors/Starlike_councilors.txt`

**变更说明:**
- `councilor_curator_copan` + `councilor_copan_administrator` → 合并为 `councilor_huixu`
- `councilor_copan_scientist` → `councilor_yinghuo`
- `councilor_copan_commander` → `councilor_luanyu`
- modifier 值同步更新

| councilor | leader_class | 关联 civic | modifier |
|---|---|---|---|
| `councilor_huixu` | official | `civic_huixu` | country_edict_fund_add=10, country_unity_produces_mult=0.05 |
| `councilor_yinghuo` | scientist | `civic_yinghuo` | country_research_speed_mult=0.05, country_anomaly_research_speed_mult=0.2 |
| `councilor_luanyu` | commander | `civic_luanyu` | country_naval_cap_mult=0.1, country_leader_exp_gain_mult=0.15 |

- [ ] **Step 1: 读取当前 Starlike_councilors.txt**

```bash
Read: Starlike/common/governments/councilors/Starlike_councilors.txt
```

- [ ] **Step 2: 完全重写文件内容**

```pdx
# 一体汉和内阁 — COPAN Council
# 三个派系内阁职位

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

# ── 辉夙委员 ── Huixu Councilor（辉夙派）
# 博物馆成员出身，统筹博物传承与行政事务
councilor_huixu = {
    leader_class = {
        official
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_huixu
    modifier = {
        country_edict_fund_add = 10
        country_unity_produces_mult = 0.05
    }
    icon = "GFX_icon_councilor_curator"
}

# ── 荧惑科学总监 ── Yinghuo Science Director（荧惑派）
# 荧惑派科学官，出身火星重建时期的科研领袖群体
councilor_yinghuo = {
    leader_class = {
        scientist
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_yinghuo
    modifier = {
        country_research_speed_mult = 0.05
        country_anomaly_research_speed_mult = 0.2
    }
    icon = "GFX_icon_councilor_scientist"
}

# ── 鸾羽舰队总长 ── Luanyu Fleet Commander（鸾羽派）
# 鸾羽号武官出身，指挥一体汉和军事力量
councilor_luanyu = {
    leader_class = {
        commander
    }
    possible = {
        origin = { value = origin_homing }
    }
    civic = civic_luanyu
    modifier = {
        country_naval_cap_mult = 0.1
        country_leader_exp_gain_mult = 0.15
    }
    icon = "GFX_icon_councilor_commander"
}
```

- [ ] **Step 3: 验证文件写入成功**

```bash
Grep: "councilor_huixu" in Starlike/common/governments/councilors/Starlike_councilors.txt
# 预期：1条
Grep: "councilor_yinghuo" in Starlike/common/governments/councilors/Starlike_councilors.txt
# 预期：1条
Grep: "councilor_luanyu" in Starlike/common/governments/councilors/Starlike_councilors.txt
# 预期：1条
Grep: "civic = civic_huixu|civic_yinghuo|civic_luanyu" in Starlike/common/governments/councilors/Starlike_councilors.txt
# 预期：3条
```

---

## Task 3: 同步双语本地化

**涉及文件:**
- `localisation/l_english/Starlike_l_english.yml`
- `localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

**变更说明:**
- 删除旧 key：`civic_copan_administrator*`、`civic_copan_scientist*`、`civic_copan_commander*`、`councilor_copan_administrator*`、`councilor_copan_scientist*`、`councilor_copan_commander*`、`civic_huisu_museum*`、`councilor_curator_copan*`
- 新增 key：`civic_huixu*`、`civic_yinghuo*`、`civic_luanyu*`、`councilor_huixu*`、`councilor_yinghuo*`、`councilor_luanyu*`

### 英文本地化 Key 清单

| Key | 英文文本 |
|---|---|
| `civic_huixu` | "Huixu Faction" |
| `civic_huixu_desc` | "The Huixu Faction comprises the core crew of Project Homing — the curators and archivists who carried civilization's memory through the void. They govern with an emphasis on heritage, unity, and administrative efficiency." |
| `civic_huixu_effects` | "§H+25%§! Archaeology Speed\n§H+15%§! Relic Energy Production\n§H+15%§! Unity Production\n§H+10§! Edict Fund\n§R-10%§! Naval Capacity" |
| `civic_huixu_negative_effects` | "" |
| `civic_yinghuo` | "Yinghuo Faction" |
| `civic_yinghuo_desc` | "The Yinghuo Faction: the scientists who rebuilt civilization on Mars after Earth was consumed by stellar assimilation. Their current administrator embodies their collective wisdom, guiding the Commonwealth toward scientific discovery." |
| `civic_yinghuo_effects` | "§H+10%§! Research Speed\n§H+20%§! Survey Speed\n§H+20%§! Anomaly Research Speed\n§H+15%§! Science Ship Survey Speed\n§R-1§! Leader Pool Size" |
| `civic_yinghuo_negative_effects` | "" |
| `civic_luanyu` | "Luanyu Faction" |
| `civic_luanyu_desc` | "The Luanyu Faction: military officers from the Luanyu vessel, originally stationed to oversee Project Homing. Transported to the stars by accident, they now protect the Commonwealth with disciplined strength." |
| `civic_luanyu_effects` | "§H+20%§! Naval Capacity\n§H+25%§! Leader Experience Gain\n§H+15%§! Army Damage\n§R-5%§! Unity Production" |
| `civic_luanyu_negative_effects` | "" |
| `councilor_huixu` | "Huixu Councilor" |
| `councilor_huixu_desc` | "The Huixu Councilor oversees the Commonwealth's archival traditions and administrative machinery, representing the values of the original Project Homing crew." |
| `councilor_yinghuo` | "Yinghuo Science Director" |
| `councilor_yinghuo_desc` | "The Yinghuo Science Director leads the Commonwealth's research mandate, drawing on the scientific legacy of Mars reconstruction." |
| `councilor_luanyu` | "Luanyu Fleet Commander" |
| `councilor_luanyu_desc` | "The Luanyu Fleet Commander commands the Commonwealth's military forces, embodying the martial discipline of the Luanyu vessel." |

### 中文本地化 Key 清单

| Key | 中文文本 |
|---|---|
| `civic_huixu` | "辉夙派" |
| `civic_huixu_desc` | "辉夙派：归巢计划的核心船员群体，以博物馆成员和档案管理员为主。他们以传承与治理效率为执政重心，在漫长的流亡中维系着文明的记忆与认同。" |
| `civic_huixu_effects` | "§H+25%§! 考古速度\n§H+15%§! 遗珍能量产出\n§H+15%§! 凝聚力产出\n§H+10§! 法令基金\n§R-10%§! 舰船容量" |
| `civic_huixu_negative_effects` | "" |
| `civic_yinghuo` | "荧惑派" |
| `civic_yinghuo_desc` | "荧惑派：地球被星尘同化为恒星后，在火星重建时期的科学官群体。星火相传，荧荧不灭。当前执政官即出身于此，其支持者自称荧惑派，以集体智慧引领文明存续。" |
| `civic_yinghuo_effects` | "§H+10%§! 科研速度\n§H+20%§! 勘测速度\n§H+20%§! 异常研究速度\n§H+15%§! 科学船勘测速度\n§R-1§! 领袖储备容量" |
| `civic_yinghuo_negative_effects` | "" |
| `civic_luanyu` | "鸾羽派" |
| `civic_luanyu_desc` | "鸾羽派：鸾羽号武官群体，原为帝国监视归巢计划而派遣的武装力量。意外降临群星位面后，他们以铁律武力护佑文明，崇尚军事秩序与力量。" |
| `civic_luanyu_effects` | "§H+20%§! 舰船容量\n§H+25%§! 领袖经验获取\n§H+15%§! 陆军伤害\n§R-5%§! 凝聚力产出" |
| `civic_luanyu_negative_effects` | "" |
| `councilor_huixu` | "辉夙委员" |
| `councilor_huixu_desc` | "辉夙委员统筹一体汉和的博物传承与行政事务，代表归巢计划原始船员的价值观。" |
| `councilor_yinghuo` | "荧惑科学总监" |
| `councilor_yinghuo_desc` | "荧惑科学总监领导一体汉和的科研使命，承继火星重建时期的科学遗产。" |
| `councilor_luanyu` | "鸾羽舰队总长" |
| `councilor_luanyu_desc` | "鸾羽舰队总长指挥一体汉和的军事力量，体现鸾羽号的铁律武力传统。" |

- [ ] **Step 1: 读取两个本地化文件**

```bash
Read: localisation/l_english/Starlike_l_english.yml
Read: localisation/l_simp_chinese/Starlike_l_simp_chinese.yml
```

- [ ] **Step 2: 英文文件 — 删除旧 key，追加新 key**

删除以下旧 key（通过 Edit 工具搜索并替换为空）：
- `civic_copan_administrator` / `_desc` / `_effects` / `_negative_effects`
- `civic_copan_scientist` / `_desc` / `_effects` / `_negative_effects`
- `civic_copan_commander` / `_desc` / `_effects` / `_negative_effects`
- `councilor_copan_administrator` / `_desc`
- `councilor_copan_scientist` / `_desc`
- `councilor_copan_commander` / `_desc`
- `civic_huisu_museum` / `_desc` / `_effects` / `_negative_effects`
- `councilor_curator_copan` / `_desc`

在文件末尾追加新的英文 key（见上方清单）。

- [ ] **Step 3: 中文文件 — 删除旧 key，追加新 key**

删除以下旧 key：
- `civic_copan_administrator*`、`civic_copan_scientist*`、`civic_copan_commander*`
- `councilor_copan_administrator*`、`councilor_copan_scientist*`、`councilor_copan_commander*`
- `civic_huisu_museum`（国策名，旧 key）、`civic_huisu_museum_desc`、`civic_huisu_museum_effects`、`civic_huisu_museum_negative_effects`（全部删除，由新的 `civic_huixu*` 替代）
- `councilor_curator_copan*`

在文件末尾追加新的中文 key（见上方清单）。

- [ ] **Step 4: 验证**

```bash
Grep: "civic_huixu" in localisation/l_english/Starlike_l_english.yml
# 预期：5条（civic + _desc + _effects + _negative_effects + councilor）
Grep: "civic_yinghuo" in localisation/l_english/Starlike_l_english.yml
# 预期：5条
Grep: "civic_luanyu" in localisation/l_english/Starlike_l_english.yml
# 预期：5条
Grep: "councilor_huixu" in localisation/l_english/Starlike_l_english.yml
# 预期：2条（councilor + _desc）
# 确认旧 key 已删除：
Grep: "civic_copan_administrator" in localisation/l_english/Starlike_l_english.yml
# 预期：无
Grep: "councilor_curator_copan" in localisation/l_english/Starlike_l_english.yml
# 预期：无（英文文件）
```

---

## 技术风险 & 回退方案

| 风险 | 描述 | 回退方案 |
|---|---|---|
| `civic_huixu` modifier 叠加过多 | 合并后 modifier 较多（考古+凝聚力+政令基金），可能导致强度过高 | 将 country_archaeology_speed_mult 降至 0.15，country_unity_produces_mult 降至 0.10 |
| `civic_yinghuo` 和 `civic_luanyu` 叙事与中国文化背景 | 荧惑（火星）、鸾羽（鸾凤之羽）等为中国典故 | 描述文本已在本地化中体现，英文使用音译，玩家可见效果说明 |
| councilor 图标未更新 | councilor_huixu 沿用 `GFX_icon_councilor_curator` | 可后续替换为 `GFX_icon_councilor_administrator`，不影响功能 |

---

## 验证方式

1. **游戏内测试:** 启动 Stellaris 4.3，选 Project Homing 起源
2. **国策测试:** 国策列表应显示 3 个派系（辉夙派、荧惑派、鸾羽派），各含正确 modifier 效果描述
3. **内阁测试:** 应有 4 个内阁职位（统治者 + 辉夙委员 + 荧惑科学总监 + 鸾羽舰队总长）
4. **错误日志:** 搜索 `civic_huixu`、`civic_yinghuo`、`councilor_yinghuo` 等新 key，确认无报错
