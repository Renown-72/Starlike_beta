# 政体 auth_copan 清理实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 删除 auth_copan 政体及其所有相关引用，包括 authority 定义、council agenda、本地化 key。

**Architecture:** 清理 auth_copan 及其伴生的 council_agenda_copan_*，同步清理所有本地化 key 和文档引用。

---

## 影响范围清单

| 操作 | 文件路径 |
|------|----------|
| 删除 | `Starlike/common/governments/authorities/Starlike_authorities.txt` |
| 删除 | `Starlike/common/council_agendas/Starlike_council_agendas.txt` |
| 删除目录 | `Starlike/common/council_agendas/`（空目录） |
| 修改 | `Starlike/localisation/l_english/Starlike_l_english.yml` |
| 修改 | `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml` |
| 修改 | `CLAUDE.md` |
| 修改 | `Starlike/common/governments/CLAUDE.md` |
| 修改 | `Starlike/localisation/CLAUDE.md` |

---

## 附带发现（P2 缺口，同步修复）

| 问题 | 文件 | 现状 |
|------|------|------|
| `councilor_yinghuo` 引用 `civic_yinghuo` 但 txt 定义为 `civic_Martis` | `Starlike_councilors.txt:48` | councilors 文件引用 `civic_yinghuo`，civics 文件定义 `civic_Martis` |

---

## 任务分解

### Task 1: 删除 auth_copan 政体定义

**文件:** `Starlike/common/governments/authorities/Starlike_authorities.txt`

- [ ] **Step 1: 删除整个文件**

删除后 `common/governments/authorities/` 目录为空（mod 可不包含 authority 目录）。

### Task 2: 删除 council agenda（含 auth_copan potential）

**文件:** `Starlike/common/council_agendas/Starlike_council_agendas.txt`

- [ ] **Step 1: 删除整个文件**

```bash
# 删除空目录
rm Starlike/common/council_agendas/Starlike_council_agendas.txt
rmdir Starlike/common/council_agendas/
```

### Task 3: 清理 EN 本地化 auth_copan + council agenda key

**文件:** `Starlike/localisation/l_english/Starlike_l_english.yml`

- [ ] **Step 1: 删除 auth_copan 相关 key**

删除以下 5 个 key：
```yaml
auth_copan: "Commonwealth of Pan-Galaxy Unified"
auth_copan_DESC: "..."
auth_copan.ethic: "不包含..."
auth_copan_tt: "..."
auth_ling_new_copan: "..."
```

- [ ] **Step 2: 删除 council agenda key**

删除以下 4 个 key：
```yaml
council_agenda_copan_legacy: "Legacy of Homing"
council_agenda_copan_legacy_desc: "..."
council_agenda_copan_exploration: "Cosmic Exploration"
council_agenda_copan_exploration_desc: "..."
```

### Task 4: 清理 CN 本地化 auth_copan + council agenda key

**文件:** `Starlike/localisation/l_simp_chinese/Starlike_l_simp_chinese.yml`

- [ ] **Step 1: 删除 auth_copan 相关 key**

删除以下 5 个 key：
```yaml
auth_copan: "一体汉和"
auth_copan_desc: "一体汉和"
auth_copan.ethic: "不包含..."
auth_copan_tt: "..."
auth_ling_new_copan: "User的新时代一体汉和"
```

- [ ] **Step 2: 删除 council agenda key**

删除以下 4 个 key：
```yaml
council_agenda_copan_legacy: "归巢遗泽"
council_agenda_copan_legacy_desc: "铭记往昔，指引未来。"
council_agenda_copan_exploration: "宇宙探索"
council_agenda_copan_exploration_desc: "测绘未知，扩展视野。"
```

### Task 5: 修复 councilor civic 引用不一致（P2）

**文件:** `Starlike/common/governments/councilors/Starlike_councilors.txt:48`

- [ ] **Step 1: 将 `civic = civic_yinghuo` 改为 `civic = civic_martis`**

文本格式与 `civic_huisu` / `civic_luanyu` 保持一致（小写首字母）。

```pdx
# 行48：将
civic = civic_yinghuo
# 改为
civic = civic_martis
```

### Task 6: 同步更新模块文档

**文件:** `CLAUDE.md`, `Starlike/common/governments/CLAUDE.md`, `Starlike/localisation/CLAUDE.md`

- [ ] **Step 1: 从 CLAUDE.md 删除 auth_copan 相关描述**
- [ ] **Step 2: 从 governments/CLAUDE.md 删除 authorities 模块条目**
- [ ] **Step 3: 从 governments/CLAUDE.md 删除 council_agendas 引用**
- [ ] **Step 4: 从 localisation/CLAUDE.md 删除 auth_copan/council_agenda_copan key 清单条目**

---

## 验证方法

1. 启动 Stellaris 并启用 mod。
2. 确认无 `auth_copan` 相关控制台错误。
3. 确认 origin_homing 起源正常可用。
4. 检查 `Documents/Paradox Interactive/Stellaris/logs/error.log` 确认无 "key not found" 错误。
