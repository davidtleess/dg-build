# DG-045 — SR-09: replace six wall-clock plists with one dependency-ordered, fail-soft chain

**Layer:** 1  ·  **State:** todo  ·  **Lane:** ClaudeFable5-DG045-20260826  ·  **DG 3.0**  ·  **Tier 2**
**Source:** season sprint SR-09 (spec `docs/strategies/2026-08-20-dg-SEASON-BUILD-SPEC.md`,
D5-D6 slot, HARD land-by Fri 08-28 EOD). **Steps 1-5 pulled into the 08-26 afternoon on David's
"go" (~12:40)** — a sanctioned deviation from the calendar, worktree only, NOTHING LANDS 08-26.

**Problem (spec's own measurements):** six producers fire on independent wall-clock slots; 7 of 57
daily captures fired >15 min off-slot; market-divergence fail-closes when its FC source is a prior
date; result: permanent, unbackfillable holes (market missing 4 dates, model missing 1). One
09:00 chain running the six in dependency order ends the class.

**How we know:** spec SR-09 section (steps, edge table, proof harness); the stores' missing-date
lists confirmed live on the capture-health surface 2026-08-26.

**Done looks like (TODAY, steps 1-5):** `scripts/run_daily_chain.py` TDD — six producers in
order with argument vectors RE-DUMPED FROM THE LIVE PLISTS at build start (diff vs spec:814-836;
build from the dump); edges as a data table (one hard edge: market ← fc), no `if failed: break`;
three-state status `ok`/`failed`/`skipped_upstream_failed`; one JSON report per run at a fixed
path; true-start-vs-target recording; `--runtime-override` flag (SR-19's amendment). Proof
harness per spec (fail step 2 → others run; fail step 1 → step 5 skipped_upstream_failed).
**Done looks like (D5/D6, NOT today):** step 6 single 09:00 symlinked plist; step 7 retire FOUR
agents (b-EXCEPTION: the two SR-00 retry plists are edited, not retired) into
`ops/launchd/retired/`; step 8 close only after SR-19's D8 rollover exercise.

**RAILS (panel-imposed, David's go):**
- Report path resolves from the script's own repo root (`Path(__file__)`), never hardcoded.
- EVERY proof-harness execution passes an explicit scratch `--report-path` — no run on 08-26 may
  write the default report path (the 08-27 10:30 alert acceptance reads it).
- No full-suite or DB-opening test runs ~13:55-14:20 (SR-00 retry slots).
- EOD stop rule: stop at the last green commit, never mid-red; write a resume brief here before
  ending the day. The afternoon delivers "steps 1-5 as far as green commits reach."
- Land timing: Thu EOD ONLY IF Thu's 10:30 alert fire is scheduled+silent+heartbeat AND DG-041's
  Thu acceptance is clean; else Fri EOD per spec. Either failure is D5's first work item.

**FROM THE DG-044 LANE (carried verbatim):** (1) build the chain report writer against
`TestChainReportLines`' fixture shape — keys `steps[].name/exit_code/status`, statuses
`failed`/`skipped_upstream_failed`; a step carrying neither key is reported as unreadable.
(2) The chain runner's own label will get a class (b) line whenever any step failed (its exit
code is derived from step failures already reported one line each) — consider excluding the
chain label from class (b) when the report was readable.
**Design answer to (2), recorded now:** the chain plist's label joins the alert's pin-file
review path only if needed AFTER D5/D6 install observation — do not pre-suppress; if
double-reporting shows up on D7's alert, the fix is an exclusion in `exit_code_lines` gated on
a readable same-day chain report, built TDD then.

**Depends on:** nothing open (SR-11 landed; SR-02 tracked the plists = rollback exists).
**Overlaps:** DG-035 option (a) — DECIDED 2026-08-26: deferred post-season; chain installs as a
LaunchAgent per spec.

---

**BUILD RECORD 2026-08-26 afternoon — STEPS 1-5 COMPLETE, GREEN, PUSHED (not landed, per plan).**
Commit `bf5ba8a1` on `ticket/DG-045`, pushed to origin. TDD throughout: 26 tests in
`tests/contract/test_dg045_daily_chain_red.py`, every unit watched RED first. Full suite in the
worktree: **6203 passed / 0 failed**. Live plist vectors re-dumped 12:47 and verified IDENTICAL
to spec:814-836 before building. Spec's dry-run verification run for real: six steps in
dependency order printed, tree unchanged.

**Design decisions a future reader must know:**
1. `--dry-run` defaults TRUE (safe-by-default). **The D5/D6 plist MUST pass `--dry-run=false`**
   or the chain will print its plan every morning and capture nothing.
2. Under `--runtime-override`: generic path rewrite (any argv path under `app/data` re-roots to
   the override dir) + data-declared per-step extras — `run_feature_refresh` gains
   `--runtime-dir <override>`; `run_what_changed_report` runs `--preflight` because it exposes
   NO redirect flag (its report path is module-internal `ROOT / _REPORT_RELATIVE`). Redirecting
   it for real would need a code change to that script — deliberately out of SR-09's scope.
3. Spawn failure (OSError, e.g. missing binary) is a `failed` step with `exit_code: null`, and
   the chain carries on — fail-soft covers the failure BEFORE the exit code too.
4. Report written atomically (tmp + os.replace) at the exact path the DG-044 alert reads
   (`app/data/ops/daily_chain_latest_report.json`); cross-contract tests feed the chain's real
   report into the alert's real `chain_report_lines` in both healthy and failing shapes.

**OBSERVATION FOR D5 (not built, needs a decision):** the alert has NO freshness check on the
chain report — a chain that stops running leaves yesterday's healthy report at the fixed path
and class (c) reads it as health forever. The launchd channel covers the gap (never-attempted +
exit-code lines on the chain's label), so this is a redundancy question, not a hole; but when
the plist installs on D5/D6, consider whether class (c) should compare the report's
`chain.started_at` date to today. Small, Tier 2-adjacent, alert-side (landed code) — David's
call whether it rides with SR-09 or waits.

**RESUME BRIEF (next session / D5 Thu 08-27):** remaining = steps 6-8. (6) write
`ops/launchd/com.davidleess.dynasty-daily-chain.plist` — 09:00, absolute venv python,
`--dry-run=false`, logs under `app/data/logs/`, header per house convention, symlink install;
plist contract test per `test_capture_gap_alert_ops_scheduler.py` pattern. (7) retirement with
rollback: snapshot `launchctl list` + plutil schedules into `ops/launchd/retired/` FIRST, then
`git mv` FOUR plists (b-EXCEPTION: market + model-pvo are EDITED — strip 09:00, keep
11:30/14:00 — not retired), README in retired/. (8) SR-19's rollover exercise closes SR-09 on
D8 — not before. Land gate: Thu EOD only if Thu 10:30 alert fired scheduled+silent+heartbeat
AND DG-041 Thu acceptance clean; else Fri EOD. David runs all launchctl install/bootout
commands himself.

**2026-08-26 ~14:05 — STEPS 6-7 PREPARED on the branch (`ab522db7`, pushed), on David's "why
don't we start on tomorrow's work."** Chain plist written + contract-tested (dry-run=false
pinned; strict-XML-parse test — plutil tolerates what expat rejects); PRE-SR09 snapshots
committed (13 agents, all exit 0, taken from the live pre-change world); four plists retired
byte-identical with README/rollback; b-EXCEPTION applied (market + model-pvo → retry-only
11:30/14:00, scheduler tests updated); retirement contract test guards the layout. 67 contract
tests green. **⚠ LAND IS COUPLED TO THE SWAP:** three retired plists are live SYMLINKS — landing
before David's launchctl session + a reboot = silent loss of league/market/pvo producers.
**Thursday remaining:** morning checks (10:30 silent+heartbeat; DG-041 clean) → apply any
confirmed findings from today's adversarial review → land → ONE launchctl sitting: bootout six,
bootstrap chain + ff-playerids (DG-053), verify `launchctl list | grep -c dynasty` = 10
(13 − 6 retired + 1 chain + 1 ff-playerids + ... recount live) → step 8/SR-19 stays D8.

**2026-08-26 ~14:45 — ADVERSARIAL REVIEW COMPLETE, ALL CONFIRMED FINDINGS FIXED (`6038d2d6`,
pushed).** 29-agent review (4 lenses, per-finding adversarial verification): 16 upheld findings
deduplicating to 2 distinct majors + 3 minors + 4 missing test pins, all fixed TDD same hour.
The majors: (A) a scratch --steps-from table could silently overwrite the LIVE alert-read report
— the spec's own printed proof commands (spec:881/:886) do exactly this; the CLI now refuses
without an explicit scratch destination. **Amend the spec's proof commands when SR-09 closes.**
(B) SR-19's documented rehearsal command could not produce the real feature_refresh refusal
(nothing forwarded --season-end 2026); new repeatable `--step-extra STEP=ARG` fixes it.
**THE D8 REHEARSAL COMMAND IS NOW:**
`run_daily_chain.py --dry-run=false --runtime-override "$SCRATCH/rollover_rehearsal"
--step-extra run_feature_refresh=--season-end --step-extra run_feature_refresh=2026`
Also fixed: hard-upstream table validation (typo'd/forward edge dies at launch, not perma-skip
with exit 0); past-midnight drift computes against yesterday's target. 44 DG-045 tests green.
Branch state: bf5ba8a1 (steps 1-5) → ab522db7 (6-7 prep) → 6038d2d6 (review fixes) — READY TO
LAND Thursday after the two morning checks, coupled to David's launchctl sitting.

**⚠ THURSDAY CHECK AMENDED (2026-08-26 ~15:30, after DG-049's live dry-run):** the 10:30
scheduled run will NOT be perfectly silent — DG-044's not_loaded class correctly flags DG-053's
committed-not-installed ff-playerids plist. Expected output Thursday 10:30: exactly ONE line
("GAP com.davidleess.dynasty-ff-playerids-snapshot: present in ops/launchd but not loaded...")
plus the heartbeat. That exact line = HEALTHY (cleared by the bootstrap in the sitting). Any
OTHER line = investigate before landing.
