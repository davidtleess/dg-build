# BOARD — DG 3.0

**Current sprint:** none open

| ID | Title | Layer | State | Lane |
|---|---|---|---|---|
| DG-001 | What actually drives the Engine B projection | 3 | answered — **qualified by DG-017** | crew forensics |
| DG-002 | Walk-forward validation for RB, WR, TE | 3 | todo | — |
| DG-003 | Give the projection a distribution, not a number | 3 | todo | — |
| DG-004 | Stale leakage FAILURE report in repo root | 3 | **done — stale one-off artifact deleted; systemic guards live** | ClaudeFable5-DG004-20260825 |
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
| DG-021 | 114 players told an Engine A prior was used when none exists | 3→6 | **done — merge `b291107f` on `main`, DEPLOYED 10:21: live artifact 114→0 false rows, same 114 now honest** | ClaudeFable5-DG021-20260825 |
| DG-022 | Players with no canonical id can never be graded | **2** | **done — merge `20807368` on `main`: frozen-prediction membership lane, real-surface-QA proven** | ClaudeFable5-DG022-20260825 |
| DG-023 | Health gate labels good participation data "empty" | **1** | **done — merge `b4662707` on `main`** | Parallel-DG023-20260825 |
| DG-024 | PPG counts **ALL GAMES**, postseason included — David 2026-08-19 | 3 | **decided** | — |
| DG-025 | Ablate usage features under a scaled, tuned fit — the deciding test | 3 | todo | — |
| DG-026 | Train and test labels share the 2023 season | 3 | todo | — |
| DG-027 | Penalty chosen by random CV on repeated-player data | 3 | todo | — |
| DG-028 | "We changed nothing" check cannot see the artifacts it guards | 3 | todo | — |
| DG-029 | Is feature season 2024 absent by design or by gap? | **2** | **done — BY DESIGN, same mechanism; merge `849f3eaf` on `main`** | ClaudeFable5-DG029-20260825 |
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
| DG-041 | The inputs gate is permanently red — participation can never serve the season it is asked for | **1** | done | ClaudeFable5-DG041-20260825 |
| DG-042 | David's "all games" PPG ruling is honoured by accident, not enforced (SR-21) | 3 | **done — merge `c2b11f0a` on `main`** | ClaudeOpus5-DG042-20260825 |
| DG-043 | Player card two-lane furniture fails contrast, markup, and mobile width (pre-existing; found by DG-022 QA) | **6** | todo | — |
| DG-044 | SR-11: the daily capture gap alert — the only detection channel that will exist (absorbs DG-035 option b) | **1** | todo | ClaudeFable5-DG044-20260826 |

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

**2026-08-25, morning. David ruled: all future tickets land on `main`.** `feature/outcome-loop-week1`
is retired as a landing base; the trunk switches to `main` after today's producer window. Under that
ruling: **DG-029 landed on `main`** as merge `849f3eaf` (gate 6037 passed / 0 failed) — the answer is
**BY DESIGN, same mechanism** (`apply_inference_partition`; 2024 is the in-between season of a 2-year
outcome horizon; making it eligible is a model-definition decision, not a backfill). The close also
resolved a contradiction sitting in the 2026-08-19 trunk ledger: the Consultant lane's "gap, not a
design choice" claim (based on 2024 existing upstream) is wrong — upstream presence is consistent
with a downstream partition. **DG-004 closed**: the repo-root leakage report was an untracked one-off
probe artifact (its writer has zero production callers; `adp_sleeper` not in the current matrix);
deleted, with the pre-commit training-CSV guard and feature-gate temporal check named as the live
protection. **Recreate procedure for pre-DG-037 worktrees is proven**: remove worktree → delete local
branch (origin keeps the copy) → clear Lane → fresh `dg-work.sh` (base `main`) → cherry-pick the one
pushed commit. DG-029's cherry-pick hit one add/add conflict on the 08-19 ledger — resolved by
keeping the trunk's canonical version, NOT re-planting the worktree-local duplicate (DG-032).
**In flight:** DG-023 (handed to David's parallel session, worktree prepped on `main` at
`~/dg-wt/DG-023` with its pushed half-fix cherry-picked, 7/7 contract tests green).

**2026-08-25, later morning. DG-021 landed** as merge `b291107f` on `main` (gate green after
rebase; local full suite 6042 passed / 0 failed). The dead-window no-A-no-B arm no longer claims
an Engine A prior: `dvs_engine` stays None, the caveat says outright that no score is available,
and the player API serves a degradation notice on any modeled row with a null score. Two spec pins
of the false behavior (phase15 5.10, phase14 5.5) amended with disclosure comments.

**2026-08-25 10:21 — THE TRUNK IS ON `main` AND THE LIVE CARDS ARE HONEST.** Post-window, on
David's "go ahead": three dirty files on main-deleted paths archived
(`preserved/2026-08-25-trunk-switch/`) and stashed, `git switch main` → `b291107f` (dirty 47→25),
pvo-refresh hand-run with launchd's exact invocation → `status ok`, exit 0. Acceptance on
`universe_pvo_runtime.json` (12,225 rows): false rows **114 → 0**, old caveat **114 → 0**, honest
no-score caveat on **exactly 114** rows, all `dvs_engine=None` — the cohort is conserved. The
11:30 scheduled run is the remaining launchd-triggered confirmation. **All future producer runs
now execute from `main`.**

**The 06:15 capture is fixed and proven.** DG-040: upstream had renamed contracts `cols` →
`season_history` and added `contract_history`; the scheduled job had been 4-for-4 exit 1 since
install, and the last normalized contracts vintage was 2026-08-08 — a 16-day gap, not the 2-day
gap the err.log suggested (the log is younger than the machine). Store migrated additively, a
hand-run of launchd's exact invocation finished `status ok / exit 0`, and a 48,690-row contracts
vintage dated today sits beside the 08-08 ones. Tomorrow's scheduled run is the last confirmation.

---

**2026-08-25, mid-morning. DG-023 landed** as merge `b4662707` on `main` (gate 6067 passed / 0 failed,
ruff clean). The finding worth carrying: **the producer half already cherry-picked onto that branch
(`f2e09ab`) was inert.** It set `status: "loaded"` for participation, but the READER bucketed a stream
as empty on `status != "loaded"` **OR a null season** — and participation's frame has no `season`
column at all, so it stayed in `EMPTY:` regardless. Before/after output was byte-identical. A green
producer-side fix is not the same as a fixed surface; run the reader.

Both of this ticket's false words are gone and both were re-measured, not inherited:
`load_participation(seasons=[2025])` returns **45,184 rows across 26 columns, none named `season`**,
and `fallback_used` means a REFUSED SEASON, not a cache — `nflreadpy`'s `cache_mode` is `MEMORY`, so a
scheduled run starts cold and never serves anything from cache. The gate itself is untouched: empty,
unavailable and step-back all still degrade.

**DG-041 filed, not fixed.** Participation's upstream ceiling is `current_season - 1` *by
construction*, so it steps back on every run forever and holds `feature_refresh` degraded
permanently — even after Week 1 when the other four streams go live. A gate that is always red
carries as little information as one that is always green. Fixing it means a per-stream ceiling in
the producer's season window, which moves `source_hash`; David scoped it out of DG-023 deliberately.

**Landed alongside a live second session** (deploy/verify lane, waiting on the 11:30 pvo-refresh).
Checked first: no `dg-land.lockdir`, nothing in `doing`, zero file overlap with the trunk's 25 dirty
paths, and **`pvo_refresh` does not declare `input_provenance_field`** — only `feature_refresh` does,
of eight artifacts — so this change cannot move what that session was about to verify. `dg-land.sh`
again refused to touch the trunk and printed the note instead: **the trunk's local `main` is now 1
behind `origin/main`** and wants `git pull --ff-only` when its tree is quiet.

---

**2026-08-25 11:38 — DG-021 production-CONFIRMED, and the trunk is current.** The 11:30 scheduled
pvo-refresh fired from launchd's own trigger: exit 0, report `status ok`, artifact vintage
`15:30:02Z`, and the acceptance held — **0** rows with `dvs_engine="A"` beside a null score, **0**
old caveats, **114** honest no-score rows. Nothing about DG-021 remains open. The trunk was then
fast-forwarded `b291107f → b4662707` (overlap 0 of 25 dirty paths), so DG-023's reader fix is live
for tomorrow's 06:15+ producers and every job now runs from current `main`. Board state: DG-021,
DG-023, DG-029, DG-004 all closed today; next unclaimed picks are DG-022 (WIP on origin), DG-041
(new, layer 1), DG-020, and SR-11's slot tomorrow 08-26 — sequencing is David's call.

---

**2026-08-25 17:1x — DG-041 CLOSED (code), landed `b797ee1f`, trunk pull deliberately held.**
David's word: "go" on the DG-041-first sequencing. The fix is the ticket's cheapest shape taken
exactly: a per-stream source CEILING beside the floor in `_STREAM_LOADERS`, participation's being
the client's own formula (`get_current_season(roster=True) - 1` — verified as literally the first
line of the installed `load_participation`). TDD, RED watched (3 expected failures); one DISCLOSED
test change in the CH1 isolation fixture; gate 6,070/0, zero collection errors. Known and
disclosed: `source_hash` moves once on first run (provenance echo — frames byte-identical), then
settles. **Sequencing in force: the trunk stays on `main@a61f0fbe` through tomorrow's ~09:00 cycle
so DG-023's first scheduled production run is single-variable; trunk pulls post-window 08-26;
DG-041 live from 08-27 with seven runs before the 09-04 freeze. Production acceptance still open:
08-27 report shows participation `fallback_used=false` and `/api/health` reads `inputs_live`.**
Next unclaimed picks unchanged: DG-022 (WIP on origin, rebase care — its WIP touched a test file
DG-021 also changed), DG-020, SR-11's slot tomorrow 08-26.

---

**2026-08-25 19:3x — DG-022 CLOSED, landed `20807368`; DG-043 filed; an ID collision resolved.**
The 08-19 WIP was resumed by its own handoff, rebased (generated seams REGENERATED — the regen
caught `index.ts` about to drop main's newer types), hardened with four measured fixes (duplicate
region landmark on the real surface; TWO test files silently reading the production capture DB —
hermetic seams added, disclosed; unclosed sqlite handle; unpinned classifier arm), and **the
real-surface QA gate the WIP was blocked on now PASSED** — Tank Dell's card tells the truth on
desktop and mobile, evidence in `dg-build/preserved/2026-08-25-dg022-qa/`. Gate after rebase onto
the parallel session's DG-042: green; local suite 6,087/0, frontend 73 files/302, zero collection
errors. Whole-page QA found three PRE-EXISTING base-card defects, proven independent of DG-022 →
**DG-043** (layer 6; Tier-3-shaped, so post-freeze unless David says otherwise). **ID note:** the
a11y filing briefly carried the number DG-042 (the 08-25 product ledger says "Filed as DG-042")
before discovering the parallel session's same-evening DG-042 (PPG guard, `c2b11f0a`) — theirs
was committed first and keeps the number; the a11y ticket is DG-043 and was never committed under
042. **Trunk is now 3 merges behind origin/main** (DG-041 `b797ee1f`, DG-042 `c2b11f0a`, DG-022
`20807368`) — all still land tomorrow's post-window pull; none of the three touches a producer
except DG-041 (the planned one). Remaining unclaimed: DG-020, DG-043, SR-11's slot tomorrow.

---

**2026-08-26 05:5x — DG-044 filed and claimed for SR-11's D4 slot, on David's word.** SR-11 had no
ticket file and `dg-work.sh` requires one; DG-044 carries the claim and the build-morning brief —
the spec section (`SEASON-BUILD-SPEC.md:595-749`, MIG-1) stays the authoritative build text.
Worktree `~/dg-wt/DG-044` cut from `origin/main` `2bf91d8d` (NOT the pinned trunk), landable-clean,
lane `ClaudeFable5-DG044-20260826`; a fresh number was used deliberately — `ticket/DG-035` is
poisoned by the PR #160 collision. The trunk stays pinned at `a61f0fbe` until the post-window pull
(~10:15+); 09:00–10:15 remains hands-off for DG-023's first scheduled single-variable run. Setup
re-verified before filing: both origin tips unmoved overnight, dg-build clean at `748becf`.
