# DG-091 — the frontend program, complete. 2026-08-30

David, 2026-08-29 evening: **"to be honest the front end was not great"** — all four dimensions.
Then: *"prose and layman's language… a world-class fantasy football dynasty front end. Not a data
science, data engineering visualization"*, *"call a spade a spade, and I've given it the green
light"*, and **"start building."** Ten tickets later, in about 24 hours, it is built and live.

## What he sees now (verified on the live product, not claimed)
All seven surfaces at **1440 AND 390: zero horizontal overflow, zero raw pipeline tokens.**
Suite **402 → 575 tests**. The front page opens:
> **Today** · Morning read · **Sunday, August 30**
> **One thing needs doing: you're carrying 27 players in 26 spots, so one has to go.**
> *Worth a look —* **Your required cut: start with Rasheen Ali.**
Five-destination nav; a bottom-tab phone shell; green/red movers; Trade Lab that says
*"We don't call the winner and we don't judge whether the deal fits your team: that part is yours."*

## The ten lands
**Phase 1 (foundations):** DG-104 `67cf9f8b` · DG-076 `6bf4a155` (frontend half; backend half
deferred, no ticket) · DG-043 `555fb7e4` · DG-105 `bc065e24`
**Phase 2A (what his rulings settle):** DG-109 `59bab53e` · DG-111 `002a26bd` · DG-110 `921ec892`
**Phase 2B (the restyle):** DG-115 `d18c4610` · DG-117 `ceba40e1` · DG-116 `d9a89b87` ·
DG-114 `ba2e25a6` · DG-113 `58f5016f` · DG-118 `89110a22`

## What made it trustworthy — keep this structure
A **Tower seat** (read-only preflight, ruled every scope capture-safe), a **firewalled Studio
seat** that designed from the live rendered product only — no tickets, no source, no internal
vocabulary, and `~/frontend-studio` never touched — and crew lanes each behind a **4-refuter
panel with an honesty lens weighted heaviest.** Roughly **160 findings fixed pre-land.**

**The honesty lens justified the whole structure.** Replacing an opaque token with confident prose
is an ACT OF AUTHORSHIP, and confident prose can lie in ways a raw token cannot:
- `no_market_overlay` → *"Nobody is quoting a market price for him right now"* — **BLOCKING**, on a
  card printing "Market value 5204 · 29th overall" three lines above.
- rollup `ok` → *"Nothing needs attention"* — **BLOCKING**; the backend explicitly declines that
  claim (auxiliary degradation never degrades the root).
- DG-113 **read the producers and killed three of the design spec's own example clauses**:
  `value_delta` is day-over-day not "this month"; the payload carries rank DELTAS and no absolute
  rank; a market row carries no model value, so no sell-high claim could be made honestly.
**Rule earned: read the producer, never a plausible reading.**

## Two errors this session made, and the lesson they share
1. **36 orphaned CPU busy-loops** (its own load test; `jobs -p` returns nothing for background
   subshells in a non-interactive `zsh -c`, so the teardown killed nothing) ran 90+ minutes at
   load 63-84 and contaminated every performance reading in the window.
2. **A 4.6s API latency "defect" reported to David as fact** — then a partial retraction that was
   ALSO wrong. Clean measurement: the card renders in 199ms. Nothing was slow.
Plus, found by the closeout audit: **the claim "the sprint tail is only Tuesday" was FALSE.**
**All three are the same failure: a reading taken without checking the conditions behind it.**
That is also why the old visual gate lied (3 pass / 4 fail on one tree) and why `launchctl list`
reads exit 0 for a job that never ran. **Never trust one reading.**

## The gate that now protects it (DG-118)
3 URLs → **23 tests over every destination at both widths under BOTH motion paths**, using axe's
COMPOSITED colors (getComputedStyle disagrees and is wrong), a **content-presence assertion** that
kills the false receipt (a zero-row error screen used to pass clean: 0 violations, 0 overflow, 0
rows), the render rule re-run in a real browser, and a **COVERAGE LOCK** — adding a destination
without gating it fails the unit suite. **8 defect classes were watched failing before it landed;
8/8 deterministic runs.** It found 5 real a11y defects on its first run.

## Honest limits — what is NOT done
- **Acceptance is David's word on a season morning. Not yet given.** Everything above is evidence,
  not the verdict.
- DG-076's health-endpoint half (sha-vs-HEAD drift detection) is deferred and **has no ticket**.
- One raw token still renders inside a labelled receipt on Trade Lab
  (`cross_lane_manual_review_suppressed_market_coverage_incomplete`).
- The gate measures the DOM, not the viewport: content clipped out of a fixed-height ancestor
  still counts as present. It was run cold-start on one machine, not under CPU contention.
- Trade *partners* view is still visually unbuilt (bare definition lists, a raw three-decimal
  "Trade-fit score 2.091") — DG-116 made it conspicuous, did not fix it.
- The deployed bundle declares `source_dirty: true` — three tracked files have sat uncommitted in
  the trunk for days (incl. a real one-line pytest determinism fix). Preserved at
  `preserved/2026-08-30-trunk-dirty-state/`; landing or discarding is David's call.

---

## FINISHING PASS — David: *"do the trade partners view and the raw token"* (2026-08-30)
Both landed and live. Twelve tickets total in the program now.

**DG-119 `6f766032` — the partners view is now "Who to call".** Framed cards, the manager's name
leading, and one producer-entailed sentence per card: *"You're rebuilding and they're contending.
They're deep at RB and WR — exactly where you're thin. We and the market price 5 of their players
differently."* The raw `2.091` became a rank word on the face and moved to the receipt.
**MEASURED: 6,401px → 1,378px at 1440 (−79%); 6,926px → 3,061px at 390.** The caveat went from
11 renders to 1; the 11 "Market-influenced" badges to 0.
**The honesty core, and it is the whole reason this ticket mattered:** `activity_recency_score` is
the literal `0.0` at `league_opportunity_map.py:185` — never computed, no trade-activity input
exists. The old card printed *"How recently they've traded — 0.00"*, which **told a manager these
eleven teams had been quiet**. It now says *"We don't track trade activity, so this part scores
nothing for anybody and moves no one up or down the list."* A test couples that copy to the
producer literal, so if the field ever starts being computed the copy must change with it.

**DG-120 `b1c532d7` — receipts distinguish identifiers from messages.** IDENTIFIERS stay raw and
copyable (`model_forward_capture`, `capture_health`, `market_divergence_history` — byte-exact,
because they are addresses and rewording destroys them). MESSAGES became prose: what used to read
`roster_capacity: live_precondition_not_ok:capture_health_ok=degraded` now reads *"2 of 3 daily
capture feeds are in a bad state. Which, and why:"* followed by *"Daily model scores — missing 1
of 68 days (2026-08-12)"* with the identifier beneath it. **The render rule now FAILS a snake_case
MESSAGE inside `[data-receipt]`**, so the exemption can no longer be a hiding place.
Suite 575 → **621 tests**. Both panels' blocking findings were LATENT cases the lanes stated
plainly (posture values and receipt bases that no live payload produces today) — flagged as
unproven-on-real-data rather than claimed as verified.

**Also filed, not built:** DG-121 — the detector behind the week-stale bundle (DG-076's deferred
half, which the program closeout had named as having no ticket).
