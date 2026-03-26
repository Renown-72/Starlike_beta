# on_actions/ — 游戏事件钩子模块

[根目录](../../../CLAUDE.md) > [common/](../) > **on_actions/**

## 模块职责

定义游戏事件钩子（Game Event Hooks），在特定时机触发模组事件。

## 入口文件

`Starlike/common/on_actions/1.txt`

## 钩子清单

| Hook ID | 触发时机 | 效果 |
|---------|----------|------|
| `on_game_start_country` | 玩家国家开局时 | 触发 `event_homing.1`（自动殖民 Europa） |

## 实现细节

```pdx
on_game_start_country = {
    events = {
        event_homing.1
    }
}
```

仅对玩家生效（`is_ai = no` 在事件内检查）。

## Changelog

### 2026-03-26 — 初始版本
- 定义开局事件钩子
