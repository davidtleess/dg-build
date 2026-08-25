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
| DG-033 | A producer can abort and still be graded fresh | **1** | **done — merge `30a91c33` on `origin/feature/outcome-loop-week1`** | ClaudeOpus5-DG033-20260824 |
| DG-034 | Backup health reports `ok` while the backup is failing | **1** | **done — 52e7dfc9, hand-landed on `feature/outcome-loop-week1`** | ClaudeOpus5-DG034-20260823 |
| DG-035 | Capture chain silently does not run unless David is logged in | **1** | todo | — |
| DG-036 | A failed backup can leave the previous run's `completed` marker standing | **1** | **done — merge `de551d22` on `origin/feature/outcome-loop-week1`** | ClaudeOpus5-DG036-20260824 |
| DG-037 | No ticket can land through `dg-land.sh` — five mechanisms | process | **done — 5 mechanisms fixed, dry-run green** | ClaudeOpus5-DG037-20260823 |
| DG-038 | `dg-land.sh` cannot merge into any base that is checked out somewhere | process | **done — detached merge + `HEAD:$BASE` push; dry-run now proves the merge; gated by `tests/test-dg-land.sh`** | ClaudeFable5-DG038-20260824 |
| DG-039 | A blocked roster-capacity audit writes nothing; last week's audit stands as current | **1** | todo | — |
| DG-040 | The daily nflverse capture has never once succeeded — upstream renamed contracts `cols` | **1** | **done — merge `6b5dceb9`, deployed to the trunk, capture run green same day** | ClaudeFable5-DG040-20260824 |

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

---

**2026-08-24.** DG-033 landed as merge `30a91c33`. **Its ticket had its two artifacts backwards**,
and the correction is worth carrying: `pvo_refresh` was correctly diagnosed but has never once fired
(0 aborts against 126 `ok`), while `feature_refresh` — bundled in almost as an afterthought — is the
one that has actually failed twice, and the ticket's fix would not have seen it. A green ticket is not
the same as a closed hole; check which half of a ticket the evidence actually supports.

`roster_capacity` was investigated on David's word and deliberately NOT changed — filed as DG-039.
`run_roster_capacity_audit.py:101` guards the only write, so its `status` key can only ever say `ok`;
declaring it would have been a gate that can never fire. `what_changed` and `league_opportunity` write
no status at all, so their silence is correct. **Three of the five undeclared artifacts should stay
undeclared** — the ticket's premise that an unread `status` is always a defect does not hold.

---

**2026-08-24.** DG-036 landed. Two things a reader of this board should know:

**`dg-land.sh` still cannot land a ticket unaided — DG-038.** DG-037 fixed the five mechanisms that
stopped a worktree reaching the gate; the merge block behind the gate has a sixth, and `--dry-run`
returns before it (`dg-land.sh:100`), so a green dry run does not mean a landable ticket. DG-036's
merge was completed by hand in a detached worktree, verified byte-identical to the tested tree, and
pushed. **A failed land also leaves the temp worktree, the claim, the branch and `State: todo` behind,
and the next run silently cleans the evidence** — so an interrupted land looks like an unstarted
ticket whose lane is taken.

**The trunk's LOCAL `feature/outcome-loop-week1` is behind `origin`** (DG-036 and DG-033 both landed
by hand). A detached push
cannot move a branch that is checked out. `~/dynasty-genius-product` needs `git pull --ff-only`; it
was not run here because that trunk carries 47 dirty files from other lanes and AGENT-HOOK rule 1
says leave it alone. None of those 47 touch DG-036's four files.

**DG-034 and DG-036 both live only on `feature/outcome-loop-week1`**, which is now 38 ahead / 6 behind
`origin/main` and unmerged, 11 days from the 09-04 freeze. Nobody has said whether that branch is
meant to reach `main` before the season. It is worth someone's word.

**Naming collision worth knowing:** `origin/main`'s PR #160 merged a branch called `ticket/DG-035`,
but its content is the 2026-08-19 governance removal — nothing to do with the DG-035 capture-chain
ticket, which is still genuinely open above.

---

**2026-08-24, later.** Three state changes a reader of the 09:00 notes above should know:

**The landing pipeline works.** DG-038 is done and DG-040 landed through `dg-land.sh` unaided —
the first ticket ever to do so — against a base checked out in the trunk. A green `--dry-run` now
means "this ticket can actually land": it builds the real merge and runs `git push --dry-run`. The
nine pre-DG-037 worktrees still need remove-and-recreate before they can pass the dirty-tree gate
(safe; all nine branches are on origin).

**The trunk moved.** `~/dynasty-genius-product` was fast-forwarded to `6b5dceb9` (dirty-overlap
checked first — none of the 47 files touched the merge). Local `feature/outcome-loop-week1` is
level with origin again.

**2026-08-25.** The open question above — whether `feature/outcome-loop-week1` reaches `main`
before the season — is answered: **David ruled merge, and it merged as `d33c9896` on
`origin/main`.** Ten conflicts resolved (scorecard trio → PR #159's later refinement; the four
frontend seams REGENERATED via `npm run openapi-gen`, never hand-merged; AGENTS.md → charter +
parallel-work protocol union; AGENT_SYNC.md and the 08-18 ledger → main's landed deletion wins).
Gate on the merged tree before push: pytest **6029 passed / 0 failed / zero collection errors**
(the −294 vs the branch is main's landed deletion of 29 governance-test files, verified by
`git diff --name-status`) and frontend vitest **298/298**. Local `main` fast-forwarded to match.
The trunk still runs `feature/outcome-loop-week1` (content-identical to `main` at merge time);
whether the trunk switches to `main` and where future tickets land is an open operational choice.

**The 06:15 capture is fixed and proven.** DG-040: upstream had renamed contracts `cols` →
`season_history` and added `contract_history`; the scheduled job had been 4-for-4 exit 1 since
install, and the last normalized contracts vintage was 2026-08-08 — a 16-day gap, not the 2-day
gap the err.log suggested (the log is younger than the machine). Store migrated additively, a
hand-run of launchd's exact invocation finished `status ok / exit 0`, and a 48,690-row contracts
vintage dated today sits beside the 08-08 ones. Tomorrow's scheduled run is the last confirmation.
