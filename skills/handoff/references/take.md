# take：接手并执行 vault handoff

参数：子命令后面的路径或关键词（见 SKILL.md）。

1. 定位文件：参数是路径就直接用（相对路径从 `~/Documents/Obsidian Vault/07_AI_Docs/` 算起）；是关键词就在 `07_AI_Docs/*/handoff/` 里搜；为空就列出所有 `status: ready` 的 handoff 让我选。
2. 通读全文和它链接的笔记，确认 `repo` 和当前工作目录一致。检查 `status`：不是 `ready` 或 `in-progress` 时先告诉我，不要开始。
3. 用几句话复述 Goal、Plan & Scope、Acceptance 和 Open Questions。Open Questions 里有阻塞项就先问我。
   同时报告每个 repo 当前的分支和工作区状态，以及准备使用的 `branch`/`base`。
4. 我确认后先过 Branch Gate：在 `repo` 和 `repos` 里每个仓库都切到 `branch`，规则按 skill 执行。工作区不干净时停下来问我。切好分支后再把 `status` 改成 `in-progress`，跑 baseline test，按 Plan & Scope 执行。过程记到 Coding Log，偏离计划的地方记到 Deviations 并停下来问我。
5. 完成后在 Result 里写实际命令结果、diff 摘要和测试结果，把 `status` 改成 `review`，然后告诉我。

全局规则照常生效：只展示 diff，commit 前先问我，不改 `.env` 或 secrets，不自动安装依赖。
