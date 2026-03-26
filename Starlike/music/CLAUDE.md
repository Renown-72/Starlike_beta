# music/ — 原声音乐模块

[根目录](../../../CLAUDE.md) > **music/**

## 模块职责

收录偌星：归巢计划原声音乐，共 25 轨，分两张专辑由不同作曲家创作。

## 入口文件

`Starlike/music/StarlikeOST.txt`

## 曲目清单

### Vol.1 — Arnaud Roy (12轨)
| Track Name | File |
|------------|------|
| 01 Hui Su 辉夙 | `Arnaud Roy - Starlike 偌星 Original Soundtrack Vol.1 - 01 Hui Su 辉夙.ogg` |
| 02 Hinna 海娜 | `...02 Hinna 海娜.ogg` |
| 03 Soltos 索尔托斯 | `...03 Soltos 索尔托斯.ogg` |
| 04 Fog 弗格 | `...04 Fog 弗格.ogg` |
| 05 Dawn On Me 朝闻道 | `...05 Dawn On Me 朝闻道.ogg` |
| 06 Canossa's Expectations 卡诺莎的期待 | `...06 Canossa's Expectations 卡诺莎的期待.ogg` |
| 07 Light Sea Boundary 光海边界 | `...07 Light Sea Boundary 光海边界.ogg` |
| 08 The Graham Act 葛立恒行动 | `...08 The Graham Act 葛立恒行动.ogg` |
| 09 Planet Reclamation 星垦 | `...09 Planet Reclamation 星垦.ogg` |
| 10 Wiedburt 维德伯特 | `...10 Wiedburt 维德伯特.ogg` |
| 11 The Hover 萦回 | `...11 The Hover 萦回.ogg` |
| 12 Moonlight Route 月光航线 | `...12 Moonlight Route 月光航线.ogg` |

### Vol.2 — H-Pi (13轨)
| Track Name | File |
|------------|------|
| 01 Dive Into the Abyss 潜入深渊 | `...01 Dive Into the Abyss 潜入深渊.ogg` |
| 02 Wind of the Earth 地球之风 | `...02 Wind of the Earth 地球之风.ogg` |
| 03 Celestial Rush 天穹奔袭 | `...03 Celestial Rush 天穹奔袭.ogg` |
| 04 March On the Enemies of the SUPREME 向神的敌人前进 | `...04 March On the Enemies of the SUPREME 向神的敌人前进.ogg` |
| 05 The Sacrifice 牺牲 | `...05 The Sacrifice 牺牲.ogg` |
| 06 Praptro 普拉帕特洛 | `...06 Praptro 普拉帕特洛.ogg` |
| 07 Shine like Our Fathers 像父辈一样闪烁 | `...07 Shine like Our Fathers 像父辈一样闪烁.ogg` |
| 08 Core's Will 地核意志 | `...08 Core's Will 地核意志.ogg` |
| 09 Rule the Stars 统治星空 | `...09 Rule the Stars 统治星空.ogg` |
| 10 Blood Rose Impact 血玫瑰冲击 | `...10 Blood Rose Impact 血玫瑰冲击.ogg` |
| 11 Extinguish Me 将我熄灭 | `...11 Extinguish Me 将我熄灭.ogg` |
| 12 In the Name of the Ocean 以海洋之名 | `...12 In the Name of the Ocean 以海洋之名.ogg` |
| 13 Dimensionality Reduction 降维 | `...13 Dimensionality Reduction 降维.ogg` |

## 文件状态

- OGG 文件: **全部存在** (25个 .ogg 文件)
- StarlikeOST.txt: `file =` 路径全部**已注释**（待音频文件就位后取消注释）

## 激活音乐

激活音乐时，取消对应轨道的 `file =` 行注释：

```pdx
song = { name = "Hui_Su_Name" }
file = "Arnaud Roy - Starlike 偌星 Original Soundtrack Vol.1/Arnaud Roy - Starlike 偌星 Original Soundtrack Vol.1 - 01 Hui Su 辉夙.ogg"
```

## Changelog

### 2026-03-26 — 初始版本
- 定义 25 轨音乐元数据（轨道名称）
- 预留 file 路径（注释状态）
