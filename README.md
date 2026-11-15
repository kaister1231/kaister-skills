# kaister-skills

Kaister 的个人 Agent Skills。结构为 `skills/<name>/SKILL.md`，符合 [Agent Skills](https://agentskills.io) 规范，cc-switch 和 `npx skills` 都能识别。

| Skill | 用途 | 入口 |
| --- | --- | --- |
| `handoff` | 规划 agent 与编程 agent 之间的交接：接手、续做、验收（含 Test Plan）、归档实现方案；以及程小帮产 repo `docs/handoff/` 给外部 agent | `/handoff [take\|resume\|review\|archive\|docs] <路径或关键词>` |
| `obsidian-project-docs` | Obsidian vault `07_AI_Docs/` 的项目文档布局、查找、回写 | 自动触发 |

`handoff` 合并自原 `agent-handoff`、`coding-handoff` skill 和 `/handoff`、`/handoff-review`、`/handoff-archive` 三个 command。

## 安装

### cc-switch
Skills → ⋯ → 管理仓库 → 添加：
- Repository：`<owner>/kaister-skills`（需要先推到 GitHub）
- Branch：`main`
- Skills Path：`skills`

然后在 Discover 里安装，勾选要启用的 app。

### npx skills
```bash
# 远程（GitHub / GitLab / 任意 git URL，私有仓库用本机已有的 git 认证）
npx skills add <owner>/kaister-skills -g -a claude-code
# 本地 clone
npx skills add ~/code/kaister-skills -g -a claude-code
```
需要 Node.js ≥ 22.20。

## 迁移注意
安装前先移走旧的同名或被合并的文件，避免 `/handoff` 冲突：
`~/.claude/skills/agent-handoff`、`~/.agents/skills/agent-handoff`、`~/.agents/skills/coding-handoff`（及 `~/.claude/skills/coding-handoff` 软链）、`~/.claude/commands/handoff*.md`。
