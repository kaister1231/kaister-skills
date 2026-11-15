# resume：继续一个中断的 handoff

参数：子命令后面的路径或关键词（见 SKILL.md）。适用于 `status: in-progress`（上次没做完），或 `review` 不通过后被改回 `ready` 的 handoff。

1. 定位文件：规则和 `/handoff take` 一样；参数为空时，列出所有 `status: in-progress` 的 handoff，以及 Coding Log 非空的 `ready` handoff 让我选。是全新的 `ready`（Coding Log 为空）时，提示改用 `/handoff take`。
2. 通读全文和它链接的笔记，重点看 Coding Log、Deviations、Result，以及 Open Questions / Plan & Scope 里 review 追加的问题。
3. 对账，不要只信 Coding Log：在 `repo` 和 `repos` 的每个仓库里跑
   - `git branch --show-current`、`git status --porcelain`
   - `git log --oneline origin/<base>..HEAD`、`git diff origin/<base>...HEAD --stat`，未提交的改动用 `git diff` 一并看
   报告：Coding Log 记到哪一步；代码实际做到哪一步；两者对不上的地方（日志写了但代码没有，或代码有但日志没记）。
4. 用几句话复述剩余工作：Plan & Scope 里还没完成的部分，加上 review 追加的问题。有阻塞项先问我。
5. 我确认后过 Branch Gate（规则按 SKILL.md）。不在 `branch` 上或工作区有不明改动时停下来问我，不要 stash、reset 或丢弃改动。
6. `status` 改成 `in-progress`，在 Coding Log 追加一条「resume：断点 = …」，跑 baseline test，从断点继续，规则同 `/handoff take` 第 4、5 步。

全局规则照常生效：只展示 diff，commit 前先问我，不改 `.env` 或 secrets，不自动安装依赖。
