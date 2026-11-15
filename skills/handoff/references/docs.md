
# docs：程小帮产 docs，外部 agent 读 docs

## 分工

| 环节 | 谁做 | 原因 |
| --- | --- | --- |
| 内部数据、日志、配置、看板 | 程小帮 | MCP 已授权，凭证不出本机 |
| 飞书文档、群聊、表格 | 程小帮 | 一次授权长期可用 |
| 把事实写成 docs | 程小帮 | 外部 agent 读文档即可，不需要接口 |
| 改代码、跑测试、补单测 | 外部 agent（或本机） | 它不需要任何内部 MCP |

不要给外部 agent 配内部 MCP、Cookie 或命令行凭据。它只要仓库和 `docs/handoff/`。

## 产物：`docs/handoff/`

| 文件 | 作用 | 更新方式 |
| --- | --- | --- |
| `INDEX.md` | 唯一入口：任务、状态、读的顺序、最后更新时间 | 每次更新都改 |
| `FACTS.md` | 已核实事实：结论 + 来源 + 查证时间 | 只追加 |
| `DECISIONS.md` | 开发中的确认与取舍：问题、结论、影响 | 只追加 |

可按需加 `VERIFY.md` 放验证命令和基线，不要再多加文件——外部 agent 的上下文有限。

规则：

- 事实必须有来源：链接、LogToken、表名 + 时间窗、配置文件名 + 版本。
- `FACTS.md` / `DECISIONS.md` 只追加，不改历史；结论被推翻时写新条目并注明「取代 #n」。
- 每条事实写清楚「它约束了什么」，不要只贴原文。
- 不放密钥、Cookie、密码、token。

## 让 agent 一定读得到

1. 仓库根有 `AGENTS.md` 或 `CLAUDE.md` 时，在末尾追加一行：`- 内部事实与确认见 docs/handoff/INDEX.md，动手前先读。` 不要覆盖原有内容。
2. 两个都没有时，新建一个最小 `AGENTS.md`，只放这一行。
3. 首次交接时明确说：「先读 `docs/handoff/INDEX.md`，按里面的顺序读」。
4. 后续多轮只说：「读 `docs/handoff` 里新增的条目」。

## 开发中的增量确认

开发过程冒出新问题时，用户回来问程小帮，程小帮查清后追加到 `DECISIONS.md`（格式见模板），并更新 `INDEX.md` 的最后更新时间和「当前待确认」一栏。外部 agent 下一轮读增量即可，不用重发整份文档。

事实型问题（字段口径、接口地址、日志怎么查）直接查；设计取舍给建议，但让用户拍板。

## 模板

`templates/docs/` 下有 `INDEX.md`、`FACTS.md`、`DECISIONS.md` 三个骨架，复制到仓库的 `docs/handoff/` 再填。

## 交接命令（都在仓库目录执行）

| agent | 命令 |
| --- | --- |
| Claude Code | `claude -p "先读 docs/handoff/INDEX.md，再按其完成任务" --permission-mode acceptEdits` |
| Codex | `codex exec -C <仓库路径> "先读 docs/handoff/INDEX.md，再按其完成任务"` |
| Cursor | `cursor-agent -p "先读 docs/handoff/INDEX.md，再按其完成任务"` |
| OpenCode | `opencode run "先读 docs/handoff/INDEX.md，再按其完成任务"` |

- 让 agent 自己读文件，不要把长文塞进命令行。
- 用户没指定用哪个时先问一句（这四个本机都已安装），不替他选。
- 不要用 `--dangerously-skip-permissions` / `bypassPermissions`。
- 长任务放后台跑，拿输出文件，不要前台干等。

## 边界

- 外部 agent 的结果不能直接当结论：diff 和测试结果要自己看一眼，失败如实说。
- 一个任务一个 `docs/handoff/`；不要把多个需求塞进同一份。
- 调研阶段不要整段贴日志或文档，摘结论和可核对的来源。
- 文档写完要能独立成立：外部 agent 看不到本次对话，只能看到仓库和 docs。
