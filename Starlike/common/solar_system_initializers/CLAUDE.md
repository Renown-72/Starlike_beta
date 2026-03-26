# solar_system_initializers/ — 恒星系生成模板

[根目录](../../../CLAUDE.md) > [common/](../) > **solar_system_initializers/**

## 模块职责

定义归巢计划起源专属的开局恒星系 `homing_earth`，模拟太阳系结构，包含地球（火星殖民地）、木星及其卫星等。

## 入口文件

`Starlike/common/solar_system_initializers/Homing_initializers.txt`

## 核心标识符

| 类型 | 标识符 | 说明 |
|------|--------|------|
| System | `homing_earth` | 归巢恒星系（太阳系翻版） |
| Star Flag | `Homing_Sol` | 恒星系标记 |
| Planet Flag | `Homing_Earth_flag` | 星尘化地球标记 |
| Planet Flag | `Homing_Europa` | Europa 卫星标记 |

## 恒星系结构

```
homing_earth (sc_g, usage=origin)
  ├── NAME_Sol          (pc_g_star)          - 恒星
  ├── NAME_Homing_Earth (pc_g_star)         - 星尘化地球 [Homing_Earth_flag]
  ├── NAME_Mars         (pc_city, starting)  - 火星殖民地（开局星球）
  ├── NAME_1_Ceres      (pc_asteroid)
  ├── NAME_2_Pallas     (pc_asteroid)
  ├── NAME_3_Juno       (pc_asteroid)
  ├── NAME_4_Vesta      (pc_asteroid)
  ├── NAME_Jupiter      (pc_gas_giant)
  │   ├── NAME_Io       (pc_barren)
  │   ├── NAME_Europa   (pc_ocean)  [Homing_Europa] ← event_homing.1 自动殖民
  │   ├── NAME_Ganymede (pc_ocean)
  │   └── NAME_Callisto (pc_ocean)
  ├── NAME_Saturn       (pc_gas_giant, ringed)
  │   └── NAME_Titan    (pc_ocean)
  ├── NAME_Uranus       (pc_gas_giant)
  ├── NAME_Neptune      (pc_gas_giant)      [entity=gas_giant_neptune_entity]
  │   └── NAME_Triton   (pc_ocean)
  └── NAME_134340_Pluto (pc_asteroid)
```

## 关键逻辑

### 异常埋线
- `NAME_Homing_Earth` 的 `init_effect` 创建 `anomaly_exoplanet_01_ambient_object`
- 玩家调查该星球时触发 `event_homing.2`（星尘故土事件）

### 自动殖民
- `event_homing.1` 在开局时自动殖民 Europa（月球）
- 仅对玩家生效（`is_ai = no`）

## Changelog

### 2026-03-26 — 初始化版本
- 创建 homing_earth 恒星系（完整太阳系结构）
- Europa 设为自动殖民目标
