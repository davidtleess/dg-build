# DG-136 — A capture-stage `abort()` exits 0: the chain reports green on a morning that captured nothing

**Layer:** 2 · **State:** open · **Lane:** unclaimed · **DG 3.0** · **ops / forward capture · small**
**Source:** 09:00-chain rehearsal readers (2026-09-02 ~06:00, trunk `f8995d3d`); verified against the live log by Tower; ticketed 2026-09-02 06:15 by Tower.

**Problem:** every refusal inside `capture_model_pvo_snapshot`
(`src/dynasty_genius/capture/model_forward_capture_driver.py:448-456`, the local `abort()`) is
RETURNED as a dict, not raised. `_publish_runtime` (`scripts/run_pvo_refresh.py:417-438`) stores
that dict at `report["capture_report"]` and only a *raised* exception rewrites the outer
`report["status"]` — so the outer status stays `"ok"`, `main()` returns 0 (`:742`), and
`run_daily_chain.py:242` marks the step `ok` from the exit code alone. A morning whose capture
appended zero rows is therefore indistinguishable, at the chain level, from a morning that
captured 12,226.

**It has already happened, unnoticed:** `app/data/logs/pvo_refresh.out.log` lines ~377228-377311
hold TWO 2026-08-31 runs whose report reads outer `"status": "ok"` with
`capture_report.status: "aborted"`, `aborted_reason: required_provenance_missing:…te_v3_metadata.json`.
Both exited 0. `model_forward_capture_raw` has NO 2026-08-31 rows (68 dates present since
06-24; 08-12 and 08-31 are the two holes). The "one capture day lost" finding of 09-01 was
reached by reading the DB, not by any monitor — nothing on the machine would have said so.

**Fix shape (pick one, say which):** (a) in `_publish_runtime`, after the capture call, if
`capture_report.get("status") != "ok"` set the outer `status` to `"aborted"`,
`aborted_stage: "capture"`, `aborted_reason` from the inner report, and keep
`restored_from_backup: False` (the refresh DID succeed and the runtime pair must stay published —
the comment at `:417-418` already says so; this ticket changes the *exit code*, not the rollback
rule); or (b) have the chain read `capture_report.status` from the persisted report. (a) is the
one-line honest fix and also covers the standalone `com.davidleess.dynasty-model-pvo-refresh`
label, which is where the 08-31 runs came from; (b) leaves that label green. Prefer (a).

**Anti-scope:** do not raise from inside the driver (its persisted bare-token report is the
contract that the DG-131 capture and the standalone CLI read); do not roll back the runtime pair
on a capture abort; do not touch the receipt-based catch-up guard's success test without checking
what it reads (`reference_sleep_catchup_guard`) — if it keys on exit code, a green-to-red flip
here will make it re-kick a morning that legitimately refused, which is the *correct* new
behaviour but must be known.

**Verify:** a contract test that feeds a `capture_fn` returning
`{"status": "aborted", "aborted_reason": "x"}` and asserts `_publish_runtime` returns
`status == "aborted"`, `aborted_stage == "capture"`, and the runtime pair on disk is the NEW
bytes (not restored); `main()` exit 1 on that path; the existing ok-path test unchanged.
After landing: the first morning it fires, the chain report must name the step `failed` and the
runtime pair must still carry that morning's `artifact_vintage`.
