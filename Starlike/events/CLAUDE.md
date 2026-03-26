# events/ — 事件链模块

[根目录](../../../CLAUDE.md) > **events/**

## 模块职责

定义归巢计划的事件链，通过 `event_homing.*` 命名空间实现开局自动殖民和星尘化地球探索叙事。

## 入口文件

`Starlike/events/Starlike_homing_events.txt`

## 事件清单

| ID | 类型 | 触发条件 | 效果 |
|----|------|----------|------|
| `event_homing.1` | country_event | on_game_start_country（玩家） | 自动殖民 Europa |
| `event_homing.2` | planet_event | 调查 Homing_Earth_flag 星球 | 星尘故土异常事件（调查/推迟选项） |
| `event_homing.3` | country_event | homing_earth_investigating flag | 故土回响（归档/解码选项） |
| `event_homing.4` | country_event | homing_earth_analyzed flag | 星语者遗产（接受/拒绝选项） |

## 事件链流程

```
event_homing.1 (开局自动)
  └─ 自动殖民 Europa (is_ai = no)

event_homing.2 (玩家调查星尘化地球)
  ├─ [调查] → 设置 homing_earth_investigating flag (3650天)
  └─ [推迟] → 保留，后续可重新触发

event_homing.3 (30天后触发，调查完成)
  ├─ [归档] → +500凝聚力 + homing_earth_memories 修正
  └─ [解码] → +0.15 物理/社会研究进度 → 触发 event_homing.4

event_homing.4 (180天后触发)
  ├─ [接受] → homing_legacy 修正 + 创建带特质科学家
  └─ [拒绝] → +1000 凝聚力
```

## 命名空间

```pdx
namespace = event_homing
```

所有事件必须在文件顶部声明 namespace。

## Changelog

### 2026-03-26 — 初始版本
- event_homing.1: 开局自动殖民 Europa
- event_homing.2-.4: 星尘化地球探索事件链
