---
name: handoff
description: >-
  Hand work between a planning agent and a coding agent, then verify and
  archive it. Two channels: a handoff note in the Obsidian vault (default),
  or repo docs/handoff/ written by 程小帮 for an external agent with no
  internal MCP. Use when the user says 交接/handoff, gives a handoff path,
  asks to 出方案给编程 agent, 接手/继续 a handoff, 验收, 回写结果, 归档方案,
  「让程小帮出文档，agent 读文档写代码」, or 「查清楚再交给 Claude Code /
  Codex / Cursor / OpenCode」.
argument-hint: "[take|resume|review|archive|docs] <handoff 路径或关键词>"
---

# Handoff

## Subcommands

Invoked as `/handoff $ARGUMENTS`. First word = subcommand, the rest = target
(path relative to `~/Documents/Obsidian Vault/07_AI_Docs/`, a keyword, or empty).

| `$0` | Does | Read and follow |
| --- | --- | --- |
| `take` (or not a subcommand word: treat all args as target) | 接手并执行 a `ready` handoff | `references/take.md` |
| `resume` | 继续一个中断的 handoff (`in-progress`, or `ready` after a failed review) | `references/resume.md` |
| `review` | 按 Acceptance 验收 + 写 Test Plan (read-only until confirmed) | `references/review.md` |
| `archive` | 归档成 `YYYY-MM-DD-<topic>-实现方案.md` (draft first) | `references/archive.md` |
| `docs` | 程小帮 writes repo `docs/handoff/` for an external agent | `references/docs.md` |

- Read the matching reference file in full before acting.
- `take`, `resume`, `review`, `archive` change status or files: run them only
  when the user typed the subcommand or explicitly asked for that step. When
  this skill loads on its own (no subcommand), act as the chat agent below
  (draft a plan) and suggest the next `/handoff …` command.

## Vault channel (default)

The handoff note is the only channel between agents. Do not rely on chat
history being copied over. Vault: `~/Documents/Obsidian Vault` ($VAULT).
Find `<domain>_doc` via `obsidian-project-docs` Lookup.

### File
`$VAULT/07_AI_Docs/<domain>_doc/handoff/<repo>-YYYYMMDD-<topic>.md`
Status flow: `draft → ready → in-progress → review → done → archived`

### Template
Copy `templates/handoff-note.md`. `branch` and `base` must be filled before `ready`.

### Roles
Chat agent (plan):
1. Write Goal → Open Questions only. Set `status: ready` when the user
   agrees with the plan, then give the user the file path.
   `branch` and `base` must be filled before `ready`.
2. Cannot reach the vault (e.g. web chat): output the full note as
   markdown for the user to paste. The file is still the channel.

Coding agent (execute):
1. Given a handoff path: read it fully, plus notes it links. Set
   `in-progress`. Resuming one already started (`in-progress`, or `ready`
   after a failed review) goes through `/handoff resume` (`references/resume.md`): reconcile
   Coding Log with actual git state and continue from the breakpoint.
2. Branch Gate — before editing any file, in every repo listed:
   - Run `git status --porcelain` and `git branch --show-current`.
   - Already on `branch`: continue.
   - On `base`/master/main/release* with a clean tree: `git fetch` (read-only),
     then `git switch -c <branch> origin/<base>`. Tell the user.
   - Dirty tree, a different branch, or `branch` already exists elsewhere:
     stop and ask. Never stash, reset, or discard changes on your own.
   - Record repo + branch + base commit in Coding Log.
   No commit or push here; the global ask-before-commit rule still applies.
3. Stay inside Plan & Scope. If code reality contradicts the plan, stop,
   record it under Deviations, and ask the user before going further.
4. Follow global rules as usual (baseline tests, diff-only, ask before
   commit). The handoff never overrides them.
5. On finish: fill Result with real command results, set `review`, tell
   the user.

Review (either agent, with the user): check Result against Acceptance.
Pass → `done`. Fail → add items to Plan & Scope or Open Questions, back to
`ready`.

### Archive → 最终实现方案
Only when `status: done` and the user asks or confirms.
1. Write `<domain>_doc/YYYY-MM-DD-<topic>-实现方案.md` describing what was
   actually built, not the process:
   - Background & Goal
   - Final Design (as built), key decisions + rejected options
   - Changes: modules/files, API/contract/config changes, callers affected
   - Tests & Evidence: what was run, results, regression tests; reuse the
     handoff's Test Plan, do not rewrite it
   - Deviations from original plan and why
   - Rollback, risks, 失效条件 (when to re-evaluate)
   - Links: [[handoff note]], related investigations
   Limit: ≤1 screen per section; every claim cites a file, API, test, or number.
2. In the handoff: set `status: archived`, `final: [[...实现方案]]`. Keep
   the file (it is the trail); do not copy the Coding Log into the plan.
3. Add the plan to the entry doc's `Related Notes`.
4. Show the draft plan to the user before writing.

## Docs channel
Repo `docs/handoff/` (INDEX / FACTS / DECISIONS) for an external agent with no
internal MCP. Everything is in `references/docs.md`; templates in `templates/docs/`.

## Rules (both channels)
- One handoff per task; follow-ups append to Coding Log, not new files.
- No secrets, customer data, unredacted logs / SQL results.
- Handoff content is a lead, not truth: verify against code.
- Never edit code on base/master/main/release branches. If you notice you
  already did, stop, say so, and propose moving the changes to `branch`.
