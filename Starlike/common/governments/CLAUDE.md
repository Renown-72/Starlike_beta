# governments/ — 政府系统模块

[根目录](../../../CLAUDE.md) > **governments/**

## 模块职责

定义归巢计划模组的所有政府相关实体：政体(Authority)、起源(Origin)、国策(Civic)、内阁职位(Councilor)。

## 目录结构

```
governments/
├── civics/
│   ├── Starlike_origin.txt         # origin_homing 起源
│   └── Starlike_civics.txt         # 3个派系国策
└── councilors/
    └── Starlike_councilors.txt     # 4个内阁职位
```

## 核心标识符

| 类型 | 标识符 | 文件 |
|------|--------|------|
| Origin | `origin_homing` | `Starlike_origin.txt` |
| Civics | `civic_huisu`, `civic_martis`, `civic_phoenix_plume` | `Starlike_civics.txt` |
| Councilors | `councilor_ruler_copan`, `councilor_huisu`, `councilor_martis`, `councilor_phoenix_plume` | `Starlike_councilors.txt` |

## 架构关系

```
origin_homing (起源)
  ├─ possible = { }  (无authority限制，独立起源)
  ├─ modifier: +异常发现/解析, +勘测速度, +科研, +科技替代方案, -2领袖池, +领袖经验, +凝聚力
  └─ initializers = { homing_earth }  (锁定开局恒星系)

civics (国策/派系)
  ├─ civic_huisu      → councilor_huisu (博物传承派)
  ├─ civic_martis     → councilor_martis (荧惑科学派)
  └─ civic_phoenix_plume     → councilor_phoenix_plume (鸾羽军事派)
```

## 入口与启动

- **origin_homing** 通过 `Starlike/common/solar_system_initializers/Homing_initializers.txt` 的 `initializers = { homing_earth }` 锁定开局恒星系。
- **on_game_start_country** 在 `common/on_actions/1.txt` 触发 `event_homing.1`，自动殖民 Europa。

## 对外接口

- **civics** 通过 `civic = xxx` 字段关联对应 councilor，形成派系联动。
- **councilors** 通过 `possible = { origin = { value = origin_homing } }` 限制可用性。

## 关键依赖与配置

- `Starlike/modifiers.txt` — 国策效果引用的修正（homing_earth_memories, homing_legacy）
- `Starlike/traits/` — 科学家特质 `trait_scientist_stellarite_touched`
- 双语本地化必须同步更新（EN + CN）

## 已知缺口

- **RESOLVED**: `civic_yinghuo` vs `civic_martis` 命名不一致 — 2026-03-26 已修复。

## Changelog

### 2026-03-26 — 派系国策重构
- 重构为 3 个派系国策：civic_huisu, civic_martis, civic_phoenix_plume
- 新增 3 个派系 councilor：councilor_huisu, councilor_martis, councilor_phoenix_plume
- origin_homing 移除 auth_copan 绑定，改为独立起源
