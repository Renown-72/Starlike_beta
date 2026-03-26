# councilors/ — 内阁职位模块

[根目录](../../../CLAUDE.md) > [governments/](../) > **councilors/**

## 模块职责

定义一体汉和（COPAN）的 4 个内阁职位，包括统治者和 3 个派系 councilor，每个 councilor 通过 `civic = xxx` 关联对应国策。

## 入口文件

`Starlike/common/governments/councilors/Starlike_councilors.txt`

## Councilor 清单

| Councilor | Leader Class | 关联 Civic | 效果 |
|-----------|-------------|-----------|------|
| `councilor_ruler_copan` | official/scientist/commander | — | +5%凝聚力, +5法令基金 |
| `councilor_huisu` | official | `civic_huisu` | +10法令基金, +5%凝聚力 |
| `councilor_martis` | scientist | `civic_martis` | +5%科研, +20%异常研究 |
| `councilor_phoenix_plume` | commander | `civic_phoenix_plume` | +10%舰船容量, +15%领袖经验 |

## 架构约束

- 所有 councilor 通过 `possible = { origin = { value = origin_homing } }` 绑定 origin_homing。
- `ruler_council_position = councilor_ruler_copan` 使其成为统治者专属职位。
- 每个 councilor 通过 `civic = xxx` 字段解锁对应的国策。

## Changelog

### 2026-03-26 — 派系内阁重构
- 重构为 4 个职位（ruler + 3 faction councilors）
- councilor_huisu/martis/phoenix_plume 分别关联 civic_huisu/civic_martis/civic_phoenix_plume
