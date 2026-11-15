---
repo: <repo>
repos: []          # extra repos touched, each gets the same branch
branch: feature/<topic>   # chat agent proposes; user confirms
base: master       # branch to cut from (check repo default)
status: draft
created: YYYY-MM-DD
final: ""          # filled on archive: [[YYYY-MM-DD-<topic>-实现方案]]
---
# <topic>

<!-- chat agent writes -->
## Goal
## Context / Evidence     # logs (redacted), [[related notes]], code paths
## Plan & Scope           # files/modules to change; explicitly out of scope
## Acceptance             # tests to run, expected behavior, how to verify
## Open Questions

<!-- coding agent writes -->
## Coding Log             # dated entries: what was done, findings
## Deviations             # any change vs Plan & Scope + reason; needs user OK
## Result                 # actual command output summary, diff summary,
                          # tests run + result, remaining issues
## Test Plan              # written by /handoff-review: per-API req + key
                          # params, expected effect, new downstream calls,
                          # DB/config changes; for developers only
