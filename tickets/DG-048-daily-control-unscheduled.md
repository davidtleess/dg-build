# DG-048 — Layer 1 Daily Control exists, works, and nothing schedules it (stale since Aug 8)

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** 2026-08-26 six-layer completion audit (L1 auditor); facts re-verified same day.

**Problem:** `scripts/run_layer1_daily_control.py` — the manifest/preflight/execute control plane
for the capture layer — has no launchd plist and is invoked by nothing. Its report
(`app/data/ops/layer1_daily_control_latest.json`) was last written **2026-08-08**, 18 days ago.
A control surface that silently stopped being consulted is worse than none: anything still
reading that marker sees a healthy Aug-8 world.

**How we know:** `grep -l daily_control ops/launchd/*.plist` → no matches; marker mtime Aug 8
14:33; verified 2026-08-26.

**Done looks like:** a decision first, then the mechanics. DECIDE (David): is Daily Control still
the intended control plane, or has SR-11's alert + capture-health surface superseded it? If
superseded: retire it loudly (archive the script's role in the ledger, delete/mark the stale
marker so nothing can read Aug-8 health as current). If still wanted: schedule it (post-SR-09 it
should be a chain step or read the chain report, not a 13th wall-clock plist) and give its marker
freshness the same SR-11 treatment as every other producer. Either answer beats today's limbo.

**Tier:** decision + small ops change. Post-freeze unless David rules it into remaining slack.
**Edge distance: FOUNDATION.**
