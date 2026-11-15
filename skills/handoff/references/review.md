# review：按 Acceptance 验收（只读，结论需用户确认）

参数：子命令后面的路径或关键词（见 SKILL.md）。

1. 定位文件：规则和 `/handoff take` 一样；参数为空时，列出所有 `status: review` 的 handoff 让我选。`status` 不是 `review` 时先告诉我。
2. 核对分支：每个 repo 都应该在 `branch` 上，并且是从 `base` 切出来的。用 `git log --oneline origin/<base>..HEAD` 和 `git diff origin/<base>...HEAD --stat` 查看实际改动，未提交的改动用 `git status` / `git diff` 一并看。
3. 逐条核对，不要只读 Result 就下结论：
   - Acceptance：每一条都标成 pass / fail / 未验证，并附证据（命令输出、test 名、代码位置）。能本地跑的 test 就重跑，跑不了的写明原因。
   - Scope：实际改动的文件有没有超出 Plan & Scope；超出的部分是否已经写在 Deviations 里，并得到我的确认。
   - 风险：API 契约、DB、配置、权限有没有变化，受影响的调用方有没有检查；有没有误改 `.env` 或 secrets，有没有新增依赖、硬编码的 key 或 debug 代码。
   - 记录：Coding Log 里有没有记分支和 base commit；Result 写的是不是实际运行结果。
4. 给出结论：通过，或者不通过加问题清单（按严重程度排序，每条都写明位置和建议）。在这一步只读，不改代码。
5. 生成建议测试方案（Test Plan）：只给开发看，读完要知道怎么测。所有内容只从本次 diff 推导，diff 没碰的参数和接口不写。后端按下面的结构写：
   - **接口测试**：每个受影响的接口一节，标题写接口名和 `METHOD path` 或 SOA 方法名。
     - Req：最小 JSON 骨架，只列关键参数。本次新增或修改的参数标【改动】，上线后会改变生产已有行为的参数标【影响存量】。值用示例或脱敏值。
     - 场景：新行为、【影响存量】的回归场景、关键边界（开关关闭、空值、灰度外），每个场景一行。
     - 预期：怎么判断已经正确生效，比如看哪个字段、哪种取值、status/错误码，或者哪条 log 关键字。不写完整的 response。
   - **新增下游调用**：本次新调用了哪个接口、在什么条件下触发、预期发出的 req 里有哪些关键字段，以及怎么观察（log 关键字、trace、mock）。
   - **DB / Config 变化**：表或 key、变更前后的值、怎么验证（只读 SQL、配置平台查看），以及回滚方式。
   - **本地测不了的**：列出来，写明原因和替代办法（FAT 环境、灰度观察的指标）。
   篇幅控制在每个接口一屏以内。没有相关内容的栏目写「无」，不要凑内容。
6. 等我确认：
   - 通过：把 Review 结论写进 handoff 的 Result，Test Plan 写进 `## Test Plan`，`status` 改成 `done`，然后问我要不要归档成实现方案。
   - 不通过：把问题追加到 Plan & Scope 或 Open Questions，`status` 改回 `ready`。

不 commit、不 push、不 merge。
