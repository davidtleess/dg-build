# DG-034 — Backup health reports `ok` while the backup is failing

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** Codex Consultant read-only sweep, 2026-08-19; independently re-derived at source by the
crew Claude lane the same afternoon

**Problem:** two independent faults in the same function, either of which alone hides a failed
backup. In `app/api/routes/system_capture_health_models.py:735-776`:

1. Staleness is `now - finished_at > threshold`. A **future-dated** marker yields a negative delta,
   so it is never stale.
2. `failures` is read out of the marker and echoed to the caller, but **is never appended to
   `reasons`** — while the returned status is literally `"degraded" if reasons else "ok"`.

So a marker with `status="completed"`, `sha256_verified=true`, a future `finished_at` and a
non-empty `failures` list returns **`status="ok"`, `reasons=[]`**.

**How we know:** read `system_capture_health_models.py:748-776`. `reasons` is appended to for
staleness, non-`completed` status and unverified sha256 — and never for `failures`, which is
collected at :756-761 and passed only into the echo at :774.

**Done looks like:** a marker carrying failures cannot read `ok`, and a `finished_at` in the future
is itself a degraded state rather than a free pass. A test asserts both.

**Depends on:** nothing.

---

**Notes**
`02` §Standing Infrastructure ruling 3 is explicit: *"Silence is not success. A missed or failed run
must surface, never pass silently."* The backup is the product's disaster floor — the single copy of
the PIT capture stores, model artifacts and operational databases.

A third finding from the same sweep — backup failure disappearing from two aggregate health surfaces
(`system_tier_readiness.py:75-101`, `system_health.py:110-141`) — is **not yet independently
reproduced** and is deliberately not filed until someone measures it.
