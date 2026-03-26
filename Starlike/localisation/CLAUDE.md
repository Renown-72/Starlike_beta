# localisation/ — 本地化模块

[根目录](../../../CLAUDE.md) > **localisation/**

## 模块职责

提供模组所有文本的双语支持（英文 + 简体中文）。所有游戏内显示文本必须同时存在于两个文件中。

## 目录结构

```
localisation/
├── l_english/
│   └── Starlike_l_english.yml
└── l_simp_chinese/
    └── Starlike_l_simp_chinese.yml
```

## 本地化 Key 清单

### 音乐轨道 (25条)
- Vol.1 (Arnaud Roy): Hui_Su_Name ~ Moonlight_Route_Name (12条)
- Vol.2 (H-Pi): Dive_Into_The_Abyss_Name ~ Dimensionality_Reduction_Name (13条)

### 起源与恒星系
- `origin_homing` / `origin_homing_desc`
- `origin_tooltip_projece_homing_effects` / `origin_tooltip_project_homing_negative_effects`
- `homing_earth_NAME` / `homing_earth_DESC`
- `NAME_Homing_Earth`

### 国策 (3派系)
- `civic_huisu` / `civic_huisu_desc` / `civic_huisu_effects` / `civic_huisu_negative_effects`
- `civic_martis` / `civic_martis_desc` / `civic_martis_effects` / `civic_martis_negative_effects`
- `civic_phoenix_plume` / `civic_phoenix_plume_desc` / `civic_phoenix_plume_effects` / `civic_phoenix_plume_negative_effects`

### 内阁职位 (4个)
- `councilor_ruler_copan` / `councilor_ruler_copan_desc`
- `councilor_huisu` / `councilor_huisu_desc`
- `councilor_martis` / `councilor_martis_desc`
- `councilor_phoenix_plume` / `councilor_phoenix_plume_desc`

### 事件链
- `event_homing.2.title` / `event_homing.2.desc` / `event_homing.2.opt.investigate` / `event_homing.2.opt.defer`
- `event_homing.3.title` / `event_homing.3.desc` / `event_homing.3.opt.archive` / `event_homing.3.opt.decode`
- `event_homing.4.title` / `event_homing.4.desc` / `event_homing.4.opt.accept` / `event_homing.4.opt.decline`

### 修正与特质
- `homing_earth_memories` / `homing_earth_memories_desc`
- `homing_legacy` / `homing_legacy_desc`
- `trait_scientist_stellarite_touched` / `trait_scientist_stellarite_touched_desc`

### 其他
- `SL_LABEL` — 辉夙星人类文明博物馆标签

## 格式规范

```yaml
l_english:
  key: "Value text"
  key_with_newline: "Line1\nLine2"
  key_with_color: "§HHighlighted§! normal"
```

注意：Stellaris YAML 解析以冒号后的内容为值，文本无需额外引号。

## 已知缺口

（无）

## Changelog

### 2026-03-26 — 派系国策本地化更新
- 新增 3 派系国策完整本地化 (Huixu/Martis/Luanyu)
- 新增 4 councilor 完整本地化
