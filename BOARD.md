# BOARD — DG 3.0

**Current sprint:** none open

| ID | Title | Layer | State | Lane |
|---|---|---|---|---|
| DG-001 | What actually drives the Engine B projection | 3 | answered — **qualified by DG-017** | crew forensics |
| DG-002 | Walk-forward validation for RB, WR, TE | 3 | todo | — |
| DG-003 | Give the projection a distribution, not a number | 3 | todo | — |
| DG-004 | Stale leakage FAILURE report in repo root | 3 | todo | — |
| DG-005 | Separate player talent from team environment | 3 | todo | — |
| DG-006 | Model the career arc as a sequence | 3 | todo | — |
| DG-007 | Learn when to act, not just what things are worth | 3 | todo | — |
| DG-008 | Opportunity Delta · Market Disconnect · Efficiency Sustainability | 3 | todo | — |
| DG-009 | Regime shifts — new coach, scheme, quarterback | 3 | todo | — |
| DG-010 | Position-specific age curves | 3 | todo | — |
| DG-011 | Stress-test outside the training distribution | 3 | todo | — |
| DG-012 | News, injury reports and pressers as features | **1** | todo | — |
| DG-013 | A feature store you can query as-of a date | **2** | todo | — |
| DG-014 | Deployed models were fit on 2018–2021 only | 3 | todo | Davids-Air-15594 |
| DG-015 | Model card contradicts the training code | 3 | todo | Davids-Air-37360 |
| DG-016 | TE "validation report" doesn't say what it validated against | 3 | todo | — |
| DG-017 | We validate a scaled model and deploy an unscaled one | 3 | **confirmed** | — |
| DG-018 | Standing measurement: does the model beat the market? | 3 | todo | — |
| DG-019 | Market appears to over-disperse by ~2× | 3 | todo | — |
| DG-020 | Get more than four market snapshots | **1** | todo | Davids-Air-63412 |
| DG-021 | 114 players told an Engine A prior was used when none exists | 3→6 | todo | CodexTeam20260819 |
| DG-022 | Players with no canonical id can never be graded | **2** | todo | CodexTeam20260819 |
| DG-023 | Health gate labels good participation data "empty" | **1** | todo | ClaudeCrew20260819 |
| DG-024 | PPG counts **ALL GAMES**, postseason included — David 2026-08-19 | 3 | **decided** | — |
| DG-025 | Ablate usage features under a scaled, tuned fit — the deciding test | 3 | todo | — |
| DG-026 | Train and test labels share the 2023 season | 3 | todo | — |
| DG-027 | Penalty chosen by random CV on repeated-player data | 3 | todo | — |
| DG-028 | "We changed nothing" check cannot see the artifacts it guards | 3 | todo | — |
| DG-029 | Is feature season 2024 absent by design or by gap? | **2** | todo | Davids-Air-38585 |
| DG-030 | Compare model families to each other, not just to naive | 3 | todo | — |
| DG-031 | Salvage the outcome resolver and Coverage contract | 1 → 3 | **done — merged in PR #159** | CodexCrew20260819 |
| DG-032 | Six ledger trees, three versions of the same day; reconcile the channel | process | todo | — |
| DG-033 | A producer can abort and still be graded fresh | **1** | todo | — |
| DG-034 | Backup health reports `ok` while the backup is failing | **1** | **done — 52e7dfc9, hand-landed on `feature/outcome-loop-week1`** | ClaudeOpus5-DG034-20260823 |
| DG-035 | Capture chain silently does not run unless David is logged in | **1** | todo | — |
| DG-036 | A failed backup can leave the previous run's `completed` marker standing | **1** | todo | — |
| DG-037 | No ticket can land through `dg-land.sh` — five mechanisms | process | **done — 5 mechanisms fixed, dry-run green** | ClaudeOpus5-DG037-20260823 |

DG-001 through DG-011 came from the independent consultant brief of 2026-08-18, except DG-004,
which Tower found while checking evidence for DG-002.

DG-014 and DG-016 came from crew forensics on 2026-08-18 and were verified independently by Tower
the same night. DG-015 fell out of that verification.

DG-012 and DG-013 also came from that brief but are **not** layer-3 work and are stamped where they
belong.

---

**Dropped**

| ID | Title | Why |
|---|---|---|
| — | | |

---

**Board hygiene, 2026-08-23.** The seven Lane cells above were rebuilt from the ticket files, which
are the side the tooling reads (`dg-work.sh:39`) and therefore the correct side. They had read `—`
here while the ticket files named live lanes — a reader picking work off this board could have
collided with a claimed worktree.

**One conflict NOT resolved, because it is not an agent's to resolve:** DG-017 reads `confirmed` here
and `todo` in `tickets/DG-017-validation-tests-a-different-model.md`. Both were written by hand and
neither cites which is current. Someone who knows the work should say.

All nine `ticket/DG-*` branches are on `origin` as of today. Six worktrees (DG-014/020/021/022/023/029)
still predate the DG-037 tooling fix and cannot pass `dg-land.sh`'s dirty-tree gate; everything in
them is now pushed, so remove-and-recreate is safe.
