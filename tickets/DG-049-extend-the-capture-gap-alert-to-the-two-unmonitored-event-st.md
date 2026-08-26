# DG-049 — Extend the capture-gap alert to the two unmonitored event streams (SR-10b's deferred scope)

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Edge distance:** foundation  ·  **Size:** 2d
**Source:** 2026-08-26 six-layer completion audit → mapping fleet; roadmap RATIFIED by David
same day ("3. yes"). Full sequence context: `~/dg-build/ROADMAP-LAYERS.md`.

**In-season eligible under the freeze rule (capture-critical) — the one backlog item that may
land mid-season.**

**Problem:** SR-11's gap alert — the only detection channel — registers only the 3 store entries in capture_cadence.json; league_transactions and nflverse_usage stay unmonitored all season because the cadence analyzer has no event-stream store kind (bursty streams with legitimately quiet days do not fit daily-cadence semantics — the spec's own stated reason for deferring SR-10b). Gaps in these stores are permanently unbackfillable, and the risk window is widest right now: SR-09 replaces 13 proven plists with a brand-new chain whose bug would burn silent holes in exactly these stores — the L1 audit's named biggest risk. This is capture-critical, so it is the one L1 backlog item eligible to land during the season under the freeze rule; SR-10b is listed under the spec's 'WHAT HE DOES NOT GET', so no ticket or sprint slot covers it.

**How we know:** docs/strategies/2026-08-20-dg-SEASON-BUILD-SPEC.md:101 ('the cadence analyzer cannot hold them without a new store kind. Those two stores stay unmonitored this season'); app/config/capture_cadence.json (config_version 2, 3 store_id entries incl. fc_forward_capture); scripts/run_capture_gap_alert.py (store registration reads that config); audit L1 biggest_risk

**Done looks like:** the missing scope above exists, runs, and is test-gated; see the roadmap's
layer sequence note for ordering constraints before starting.
