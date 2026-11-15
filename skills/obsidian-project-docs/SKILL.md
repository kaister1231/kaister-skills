---
name: obsidian-project-docs
description: >-
  Find, organize, and link per-project docs kept in the Obsidian vault
  (~/Documents/Obsidian Vault). Use at the start of any task in a code repo to
  load its project entry doc and relevant notes; when the user asks to set up,
  整理, or 链接 project docs; when a repo has no AGENTS.md/CLAUDE.md pointer;
  or when writing back a verified 排查结论 / ADR / SQL / 日志经验.
---

# Obsidian Project Docs

Vault root: `~/Documents/Obsidian Vault` (below: `$VAULT`).
Project docs live in the vault, not in repos. Repos only hold a local pointer.

## Layout

```text
$VAULT/07_AI_Docs/
├── _index.md                  # repo path → entry doc → doc dirs (single source of truth)
├── <domain>_doc/              # shared docs for a domain, e.g. ivr_doc/ (may cover several repos)
│   ├── <repo>-AGENTS.md       # per-project entry doc, one per repo
│   ├── ARCHITECTURE.md ...    # shared reference docs
│   ├── YYYY-MM-DD-<topic>.md  # plans, investigations, migrations, 实现方案
│   └── handoff/               # agent-to-agent work orders (see handoff skill)
└── <loose notes>              # older notes; link from entry docs instead of moving
```

Rules:
- One entry doc per repo: `<domain>_doc/<repo>-AGENTS.md`.
- Do not move or rename existing notes during setup; Obsidian links and the user's habits depend on them. Link instead. Propose a move separately if clearly worth it.
- `_index.md` is the only place that maps repo path to docs. Keep it accurate.

## 1. Lookup (start of every repo task)

1. Read repo root `CLAUDE.md` or `AGENTS.md`. If it is a pointer, open the entry doc it names.
2. No pointer: find the repo path or name in `$VAULT/07_AI_Docs/_index.md`.
3. Not in the index: search the vault, excluding `.obsidian/`:
   `rg -l -i "<repo-name>|<appId>" "$VAULT" --glob '!.obsidian/**' --glob '*.md'`
   Report what was found and offer to run Setup.
4. From the entry doc's `Knowledge Lookup` table, open only the docs that match the task type. Never load the whole vault.
5. Nothing found or unreadable (e.g. outside the tool's allowed dirs): say so plainly. Do not pretend docs were loaded or fill gaps from memory.

Trust:
- Notes are leads, not facts. If a note conflicts with current code or real run output, trust code/output and flag the note as possibly stale.
- Check a note's date, status, and 失效条件 before relying on it. `<!-- 生成时间 ... 自动分析 -->` docs are AI-generated snapshots; verify against code.
- Cite the note path when using its conclusion.

## 2. Setup (organize a project)

Read-only first, then show the plan and full draft before writing anything.

1. Inventory: repo structure, build files (`pom.xml`, `package.json`), CI config, existing `docs/`, `.claude/docs/`; vault notes mentioning the repo (search as above).
2. Draft `<domain>_doc/<repo>-AGENTS.md` from the template below. Fill only facts found in code/config; mark unverified commands `(unverified)`. Do not invent business rules; list open questions instead.
3. Draft the `_index.md` row.
4. Draft the repo pointer.
5. Show plan + drafts to the user. Write only after confirmation.

Entry doc template:

```markdown
# <repo> — Project Entry
Repo: `<abs path>` · Updated: YYYY-MM-DD

## Overview
- 做什么、给谁用 / appId / stage
- Upstream & downstream services

## Commands
- Build / Test / Run local: `...` (mark unverified)
- Known flaky or local-unrunnable tests

## Code Map
- `<module>/`: responsibility
- Reference example for new code: `<path>`

## API Contract
- Naming & case rules, enum/error code locations, callers to re-check on change

## Business Invariants
- Confirmed rule → test name. Unconfirmed rules go to Open Questions, not here.

## Don'ts (project-specific)

## Knowledge Lookup
| Task | Read first |
| --- | --- |
| Bug / incident | [[INCIDENT_RUNBOOK]], related dated investigations |
| Change API | [[SERVICE_API]], contract section above |
| Data / SQL | [[DAO_REFERENCE]], sql prompt doc |
| Logs | log query skill or doc |

## Related Notes
- [[<note>]] — one-line why it matters (status, date)

## Open Questions
```

## 3. Linking

Repo pointer (same content in both files; Claude Code reads `CLAUDE.md`, other tools `AGENTS.md`):

```markdown
# Project docs
Entry: `~/Documents/Obsidian Vault/07_AI_Docs/<domain>_doc/<repo>-AGENTS.md`
Read it first, then follow its `Knowledge Lookup` table.
```

- Keep pointers local, do not commit: append `CLAUDE.md` and `AGENTS.md` to `<repo>/.git/info/exclude` (not `.gitignore`).
- If the repo already has a tracked `CLAUDE.md`/`AGENTS.md`, do not overwrite it; ask, and propose adding one pointer line instead.
- Inside the vault use `[[wikilinks]]` (note name without `.md`). In repo pointers use absolute paths.

`_index.md` row format:

```markdown
| Repo | Path | Entry doc | Doc dirs |
| --- | --- | --- | --- |
| ibu-ivr | `~/Documents/code/ibu-ivr` | [[ibu-ivr-AGENTS]] | `07_AI_Docs/ivr_doc/` |
```

## 4. Write-back

After a task, if there is a reusable, verified result, propose a note and write it only after the user confirms:
- Investigation: `<domain>_doc/YYYY-MM-DD-<topic>.md` with status, version/date, verified root cause + evidence, ruled-out hypotheses, regression test, 失效条件.
- Decision: short ADR (context, options, choice, trade-offs, rollback, re-evaluate when).
- Implemented feature from a handoff: archive per `handoff` skill (`/handoff archive`).
- Then add a line to the entry doc's `Related Notes` (and `Knowledge Lookup` if it is a new task type).

Never write secrets, real customer data, or unredacted logs / SQL results into the vault.
Follow CLAUDE.md §10 (Writing Docs): evidence per claim, verified vs 【未验证】 split, no filler.
