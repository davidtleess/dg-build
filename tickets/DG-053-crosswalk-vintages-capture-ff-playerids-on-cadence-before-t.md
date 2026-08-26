
---

**✅ BUILT AND LANDED 2026-08-26 (merge `34a9a970` on `main`, pre-freeze per David's "2. pre
freeze"), trunk pulled.** Minimal-scope option taken: `scripts/run_ff_playerids_snapshot_capture.py`
— nflreadpy loader → deterministic canonical hash (row/key order immune) → append-only
content-addressed store `app/data/identity_snapshots/` + `latest.json` + marker; idempotent no-op
on unchanged content; fetch failure = loud failed marker + exit 1; touches NO consumer (the frozen
2026-05-16 snapshot stays where production reads it). 9 tests RED-first; plist
`com.davidleess.dynasty-ff-playerids-snapshot` (06:45) committed-not-installed —
**install rides Thursday's launchctl sitting:**
`ln -s .../ops/launchd/com.davidleess.dynasty-ff-playerids-snapshot.plist ~/Library/LaunchAgents/`
then `launchctl bootstrap gui/501 ~/Library/LaunchAgents/com.davidleess.dynasty-ff-playerids-snapshot.plist`.
First scheduled fire Fri 06:45 (or Thu if installed before 06:45 — it wasn't). The full-scope
remainder (reconciliation layers) stays post-season per the roadmap.
