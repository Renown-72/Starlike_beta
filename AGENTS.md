# 仓库规范 · 偌星：归巢计划 / Starlike: Project Homing

Stellaris v4.3 模组，创意工坊 ID `3480510700`。

## 1. 目录结构

仓库顶层只有 5 项：`.gitignore`、`AGENTS.md`、`README.md`、`Starlike.mod`、`Starlike/`

- `Starlike/` —— 模组本体，即被游戏加载的目录
  - `common/` —— 游戏性定义，一个定义类型一个子目录，现有 29 个：
    `anomalies`、`armies`、`ascension_perks`、`buildings`、`council_agendas`、`decisions`、
    `edicts`、`event_chains`、`game_concepts`、`governments`、`message_types`、`name_lists`、
    `on_actions`、`policies`、`pop_faction_types`、`pop_jobs`、`portrait_sets`、
    `scripted_effects`、`scripted_triggers`、`situations`、`solar_system_initializers`、
    `species`、`start_screen_messages`、`static_modifiers`、`strategic_resources`、
    `technology`、`tradition_categories`、`traditions`、`traits`
  - `events/` —— 叙事脚本
  - `localisation/` —— 中英文本地化，见第 4 节
  - `gfx/` —— 美术资源（事件图、图标、载入图、立绘模型）
  - `interface/` —— 界面 `.gfx` 定义
  - `music/` —— 原声带
  - `descriptor.mod` —— 版本元数据须与根目录 `Starlike.mod` 保持一致
- `Starlike.mod` —— 模组描述文件（含本地加载路径，见第 7 节）

> `docs/` 不在仓库中：`.gitignore` 会忽略它，设计稿与实施计划等本地文档不会随克隆下载。

## 2. 开发与验证

无编译步骤，也无自动化测试。

1. 把模组安装或链接到 Stellaris 用户模组目录，在启动器中启用
2. 用 `origin_homing` 起源新建游戏，逐项验证：起始设置、地球异常、所选国策路线、派系事件、飞升解锁、地球重建
3. 改动事件、本地化、立绘或脚本化效果时，加 `-debug_mode` 启动
4. 每次运行后检查 `%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\error.log`

常用命令：

```bash
git diff --check                            # 提交前查空白符错误
rg -n "event_homing|sl_bloodline" Starlike  # 追踪事件与血脉引用（需先安装 ripgrep）
```

```powershell
Get-Content "$env:USERPROFILE\Documents\Paradox Interactive\Stellaris\logs\error.log" -Tail 100
```

## 3. 编码规范

- Paradox 脚本一律 **4 空格缩进**，块布局与相邻代码保持一致
- 标识符用小写 `snake_case`，前缀 `sl_` 或 `sl_copan_`
- 事件 ID 用 `namespace.number`。现有命名空间：`event_homing`、`sl_bloodline`、`sl_bige_catch`、`sl_bige_chat`、`sl_bige_cmt`
- 文件改名后必须同步更新引用，并用 `rg` 全库确认无残留

## 4. 本地化规范

- 文件必须是 **UTF-8 带 BOM**（字节前缀 `EF BB BF`）。漏掉 BOM 会导致中文不显示，是最高频故障
- 文件名以 `_l_english.yml` / `_l_simp_chinese.yml` 结尾
- 每个键必须在**中英两棵树里成对存在**，键名完全一致
- 保留文件头 `l_english:` / `l_simp_chinese:`
- 字符串内的引号需转义
- 目录摆放：`l_english/` 为平铺；`l_simp_chinese/` 下已有 `copan/` 子目录，新增文件请与同类文件放在一起

## 5. 版本与发布

版本号只有**一个来源**，以下三处必须同步：

1. `Starlike.mod` 的 `version` 字段
2. `Starlike/descriptor.mod` 的 `version` 字段
3. git tag

- 发版打**附注 tag**：`git tag -a vX.Y -m "说明"`
- ⚠️ `git push` **不会**上传 tag，必须显式推送：`git push origin vX.Y`
- 没有 tag 就不要发布到创意工坊

## 6. 提交规范

- Conventional Commits：`feat:` / `fix:` / `docs:` / `refactor:` / `chore:`，后接中文或英文简述
- 每次提交聚焦一件事
- 小改动直接提交 `main`；高风险重构或可能失败的功能开短命分支，做完即合
- 改事件链时，在提交信息里写明控制台复现步骤

## 7. 不要提交的内容

⚠️ `.gitignore` 是**白名单**模式：先 `/*` 忽略根目录一切，再逐个 `!` 放行。
**在仓库根目录新增任何文件或目录都会被静默忽略**，必须同时往 `.gitignore` 里加 `!文件名` 例外，否则会以为"提交不上"。

不提交：启动器路径、存档、生成类文档。

- `Starlike.mod` 里的 `path=` 指向本地 Stellaris 模组目录，是加载所必需的，**保留原样**，不要改成自己的路径后提交
- `remote_file_id` 除有意转移创意工坊所有权外不要改动
