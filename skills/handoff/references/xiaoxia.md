# xiaoxia：把 handoff 或任务交给小虾

参数：子命令后面的 handoff 路径 / 关键词，或直接的写代码 / review / 排查任务（见 SKILL.md）。

依赖本机的 `xiaoxia-bridge` skill（机器人和会话 ID 都在里面，不进本 repo）。找不到这个 skill 时停下来告诉我，不要自己猜 ID 或会话。

1. 参数为空时先让我补充任务或 handoff 路径。识别输入是 **handoff 文档**，还是直接的 **写代码、代码 Review、业务 / 日志排查**任务；类型不明确且会影响产出时先问我。
2. 参数是 handoff 路径或关键词时，按本 skill Vault channel 的规则定位并通读全文及链接笔记。新委派要求 `status: ready`；`status: in-progress` 时优先查找已有的小虾任务和结果；其他状态先告诉我。Open Questions 里有阻塞项先问我。
3. 先按 `xiaoxia-bridge` 的分工表判断是否值得外派。本机工具能秒级或分钟级完成的，告诉我更适合本机处理；我明确指定交给小虾，或任务需要机票业务知识、较大代码改造、业务代码 Review、大范围日志排查、长时间沙箱执行时再外派。
4. 所有任务都整理成一条自包含消息，包含【任务类型】【任务】【背景 / 材料】【期望产出】。handoff 任务以 Goal、Context / Evidence、Plan & Scope、Acceptance 为准。订单号、时间窗、仓库、MR、飞书文档或日志链接要写全；小虾读不到本机会话、文件和 Obsidian vault。本地材料优先给内网链接，必须传文件内容时按 skill 写入工作区再发送。不要包含 secrets 或未脱敏数据。

按任务类型补充：

- **写代码 / 通过 handoff 委派实现**
  - 写清仓库 / 模块、目标、`branch` 与 `base`、Plan & Scope、明确不做什么、Acceptance 和要跑的测试。
  - 涉及接口时写清字段、类型、枚举、错误码等契约；不允许擅自改契约、加依赖、改 DB / 配置或 secrets。
  - 明确要求小虾在沙箱里实现并运行测试，完成后**创建 MR**，返回 MR 链接、源分支 / 目标分支、实际改动摘要、测试结果和 Deviations。MR 是代码回传与验收的依据。
  - 这里允许小虾为当前任务 commit、push 源分支并创建 MR；程小帮自己不替我 commit、push 或 merge。未经我确认，不让小虾合并 MR。
  - 仓库、改造范围或验收标准缺失到会改变实现结果时，先问我，不要直接外派。
- **代码 Review**
  - 走现有「小虾群组」，@小虾同学，不走私聊；使用 `xiaoxia-bridge` 已确认的群 `chat_id` 和 @ 格式。
  - 给 MR / 分支 / commit / diff 链接，以及需求或业务背景、评审重点；只有本地代码时先整理成小虾可访问的材料。
  - 要求按严重程度输出问题，每条带文件、位置、证据和修复建议；区分确定性问题与推测，核对 API 契约、业务规则、回归风险和测试缺口。
  - Review 只读，不改代码；不要只给泛泛总结。
- **业务 / 日志排查**
  - 给订单号、时间窗、appId、已知现象、已排除项和需要核对的业务规则。
  - 要求结论附日志、报文、文档或代码证据；区分已验证事实与推测，保留日志追踪 ID。

发送与异步跟踪：

1. 按 `xiaoxia-bridge` 发送：写代码和业务排查发到小虾私聊，代码 Review 发到现有「小虾群组」并 @小虾。一条消息只发一次，不重复催进度。记下返回的 `message_id` 和本次 `chat_id`，连同任务类型、摘要和发出时间追加到 pending.jsonl。
2. 如果输入是 handoff，同时在 Coding Log 记录小虾任务、`chat_id`、`message_id` 和发出时间，并把 `status` 改为 `in-progress`。
3. 发送后立即告诉我「已交给小虾，预计十几分钟」，说明任务类型，然后结束本轮或继续其他工作；不要原地等待、连续轮询，除非我明确要求等结果。

取结果与 MR 验收：

1. 我之后询问结果时，按 pending.jsonl 中的 `chat_id` 和 `message_id` 读取对应 `thread_replies`。未完成就简报状态；失败或长时间无进展要如实说明，不把中间态包装成结论。
2. 写代码任务完成后，先确认回复里有可访问的 **MR 链接**。只有文字总结、没有 MR 时，告诉我代码尚未回传，不能据此验收或把 handoff 标成 review。
3. 拿到 MR 后，读取 MR 的实际 diff、提交和测试证据，不只信小虾的总结。按 handoff 的 Acceptance、Plan & Scope 以及 `/handoff review` 的风险清单验收：逐条标 pass / fail / 未验证并附证据，检查超范围改动、API 契约、DB / 配置、权限、新增依赖、secrets 和测试缺口。此步只读，不改代码、不 merge。
4. 如果输入是 handoff，把 MR 链接、实际 diff 摘要和验收证据写入 Result，并将 `status` 改为 `review`。验收结论先给我确认：通过后才能按规则转 `done`；不通过则把问题追加到 Plan & Scope 或 Open Questions，并转回 `ready`。
5. Review / 排查任务直接整理小虾的完整结果，保留问题清单、证据和日志追踪 ID；有附件按 skill 下载后给我路径。
