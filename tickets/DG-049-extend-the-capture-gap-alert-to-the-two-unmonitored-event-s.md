
---

**✅ BUILT AND LANDED 2026-08-26 ~15:30 (merge on `main` via dg-land full gate, trunk pulled) —
the 2d estimate closed in ~45 minutes because both producers ALREADY write atomic status markers
on every run (no producer changes needed; the estimate priced building attestation, which
existed).** Design: attestation channel — after slot+grace the marker must attest TODAY with
status ok; absent/stale/unreadable/future-dated/failed each alert once per NEW signature, persist
silently (known-holes philosophy), recovery clears so relapse is loud. Config: additive optional
`event_streams` in capture_cadence.json (fail-closed validated; the pydantic strict-mode
tuple-vs-list trap cost one RED cycle and is why the loader tests exist). State schema 2 gains
additive `event_stream_issues`. 13 tests RED-first; DG-044's alert suite + capture-health suites
green unchanged. **Every capture store now has a detection channel before the chain soaks.**

**Live dry-run acceptance produced two side findings:**
1. The alert auto-flagged DG-053's committed-not-installed plist (class not_loaded) — TRUE
   positive. **Thursday's 10:30 check expectation is AMENDED: the run should print exactly ONE
   line (the ff-playerids not-loaded line) + heartbeat until David's bootstrap clears it.**
   Recorded in DG-045's land conditions.
2. `~/dg-wt` worktrees' share map predates `league_transactions/` — the dir is absent in
   worktrees, so a worktree-rooted dry-run shows a false "no attestation marker" for it. Trunk
   truth is healthy. Housekeeping: add league_transactions to dg-work.sh's share map.
