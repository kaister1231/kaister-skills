# resume：继续一个中断的 handoff

参数：子命令后面的路径或关键词（见 SKILL.md）。适用于 `status: in-progress`（上次没做完），或 `review` 不通过后被改回 `ready` 的 handoff。

1. 定位文件：规则和 `/handoff take` 一样；参数为空时，列出所有 `status: in-progress` 的 handoff，以及 Coding Log 非空的 `ready` handoff 让我选。是全新的 `ready`（Coding Log 为空）时，提示改用 `/handoff take`；是 `review`、`done`、`archived` 时停下来告诉我，改用 `/handoff review` 或 `/handoff archive`。被打回的 `ready` 先找出上一轮的失败项。
2. 通读全文和它链接的笔记，重点看 Coding Log、Deviations、Result，以及 Open Questions / Plan & Scope 里 review 追加的问题。
3. 对账，不要只信 Coding Log：在 `repo` 和 `repos` 的每个仓库里跑
   - `git branch --show-current`、`git status --porcelain`
   - `git log --oneline origin/<base>..HEAD`、`git diff origin/<base>...HEAD --stat`，未提交的改动用 `git diff` 一并看
   报告：Coding Log 记到哪一步；代码实际做到哪一步；两者对不上的地方（日志写了但代码没有，或代码有但日志没记）。
4. 报告断点再动手，用几行说清：
   - 已完成（附 Coding Log 条目和对应文件）
   - 未完成（Plan & Scope 里还没做的条目，加上 review 追加的问题）
   - 待确认的 Deviations 和 Open Questions
   - 当前分支、工作区状态、上一次跑的 test 结果
   有阻塞项先问我。
5. 我确认后过 Branch Gate（规则按 SKILL.md）。不在 `branch` 上或工作区有不明改动时停下来问我，不要 stash、reset 或丢弃改动。
6. `status` 不是 `in-progress` 时改成 `in-progress`（已经是就不动），在 Coding Log 追加一条「resume：断点 = …」，跑 baseline test，从断点继续，规则同 `/handoff take` 第 4、5 步：
   - 只做没做的部分，不重跑已完成的工作，不重写已有结论。
   - 新进展按日期追加到 Coding Log；不新建 handoff 文件，不删改上一轮的记录。
   - 再发现偏离 Plan & Scope，记到 Deviations 并停下来问我。
7. 收尾：Result 里写这次实际的命令输出、diff 摘要和测试结果，`status` 改成 `review`，然后告诉我。

全局规则照常生效：只展示 diff，commit 前先问我，不改 `.env` 或 secrets，不自动安装依赖。
