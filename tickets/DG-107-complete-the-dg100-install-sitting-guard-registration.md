# DG-107 — Complete the DG-100 install sitting: register the vintage-sync label with the catch-up guard

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**  ·  **second half of the DG-100 sitting — David said "install it" 2026-08-30**
**Source:** DG-100's own install instruction; the bootstrap half was executed 2026-08-30 07:34 on David's word.

**Problem:** `com.davidleess.dynasty-nflverse-vintage-sync` is now BOOTSTRAPPED (15 dynasty labels, 1 calendar slot, verified 2026-08-30 07:34) but still sits in `catchup_guard.json`'s `unguarded` section, where DG-100 parked it precisely because guarding an uninstalled label makes the guard kick-fail it every 15-minute tick. Now that it is installed, that parking note is stale in the other direction: a 07:00 slot slept through would never be re-kicked, so a missed morning silently stops protecting that day's vintages.

**How we know:** `launchctl print gui/501/com.davidleess.dynasty-nflverse-vintage-sync` → `state = not running`, `runs = 0`, exactly 1 `com.apple.launchd.calendarinterval` event source (2026-08-30 07:34); `app/config/catchup_guard.json` still lists the label under `unguarded` with the migration instruction quoted in its own value.

**Done looks like:** the label moves from `unguarded` to `receipts` as the exact entry the config already names:
```json
"com.davidleess.dynasty-nflverse-vintage-sync": {
  "receipt_path": "app/data/ops/nflverse_vintage_backup_status.json",
  "timestamp_fields": ["finished_at", "started_at"]
}
```
`tests/test_catchup_guard.py` and `tests/contract/test_dg087_ops_scheduler.py` stay green (they assert every scheduled label is guarded or explained — this moves it from "explained" to "guarded"). Guard reports the label configured, not degraded.

**Depends on:** **the marker must exist before this lands.** `app/data/ops/nflverse_vintage_backup_status.json` is written only when a run completes; the one-time backfill is still running (211/1,584 objects at 07:37). Landing while the marker is absent makes the guard read today's already-passed 07:00 slot as missed and kick a duplicate run alongside the backfill — harmless (the channel is additive, `--no-clobber`, per-file verified) but pointless load. **Land after the backfill writes its marker.**

---

**Notes**
- Keep the config diff surgical — one key moved, no reformat. A `json.dumps` rewrite reflows the whole file (135 lines of noise; caught and reverted during DG-100).
- After landing, the guard's next tick should report the label with a fresh receipt and take no action. That is the acceptance: silence, plus the label present in the guard's configured set.
