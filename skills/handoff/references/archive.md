# archive：归档成最终实现方案（先出草稿，确认后写入）

参数：子命令后面的路径或关键词（见 SKILL.md）。按 SKILL.md 的 Archive 规则执行。

1. 定位文件：规则和 `/handoff take` 一样；参数为空时，列出所有 `status: done` 的 handoff 让我选。`status` 不是 `done` 时停下来，提示先跑 `/handoff review`。
2. 收集事实：handoff 全文、Deviations、Result 和 Review 结论；实际代码以 `git diff origin/<base>...<branch>` 为准（多 repo 的话逐个看）。handoff 和代码对不上时，以代码为准，并在草稿里标出差异。
3. 起草 `<domain>_doc/YYYY-MM-DD-<topic>-实现方案.md`。日期用完成当天，`<topic>` 和 handoff 保持一致。写最终做成了什么，不写过程：
   - Background & Goal
   - Final Design（as built）：关键决策和被否决的方案
   - Changes：repo/branch、模块和文件、API/契约/配置变更、受影响的调用方
   - Tests & Evidence：跑了什么、结果、回归 test
   - Deviations：和原计划的差异及原因
   - Rollback、风险、失效条件
   - Links：[[handoff 笔记]]、相关排查笔记
   不要照抄 Coding Log；不写 secrets、客户数据、未脱敏的 logs 或 SQL 结果。
   按 CLAUDE.md 第 10 节写；每节不超过一屏，Tests & Evidence 直接引用 handoff 的 Test Plan。
4. 把完整草稿发给我。目标文件已存在时先告诉我，不要覆盖。
5. 我确认后：
   - 写入实现方案；
   - 把 handoff 的 `status` 改成 `archived`，`final:` 填 `[[...-实现方案]]`，handoff 文件保留；
   - 在 repo 入口文档（`<repo>-AGENTS.md`）的 `Related Notes` 里加一行链接；多 repo 的话每个入口文档都加；
   - 检查新文件里的 `[[wikilinks]]` 都能找到对应笔记，结果报告给我。

只改 vault 里的笔记，不改代码，不 commit、不 push、不 merge。
