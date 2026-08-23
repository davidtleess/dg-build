# DG-033 — A producer can abort and still be graded fresh

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** Codex Consultant read-only sweep, 2026-08-19; independently re-derived at source by the
crew Claude lane the same afternoon

**Problem:** `pvo_refresh` and `feature_refresh` both write a terminal `status` into their reports,
and neither declares `status_field` in `app/config/report_freshness.json`. That field is **opt-in**
by design (`app/api/routes/system_health_models.py:78-97`), so with it absent the health gate grades
on **mtime alone** and never reads the status the producer already wrote. A wholly aborted run
reports `fresh`. `pvo_refresh` is `tier: core_substrate` — the artifact the entire valuation runtime
is built from.

**How we know:**
```
$ grep -n '"status": "aborted"' scripts/run_pvo_refresh.py
345, 388, 429, 492, 558        # five distinct abort sites, each writing a terminal status

$ grep -n status_field app/config/report_freshness.json
131, 148, 172                  # three artifacts declare it; pvo_refresh and feature_refresh do not
```
Both live reports currently read `status: ok`, so the gate is not lying today — it is structurally
unable to notice when it should.

**Done looks like:** an aborted `run_pvo_refresh.py` makes `/api/health` say so. The two entries
declare `status_field` and the `success_status` its validator requires, and a test writes a report
with `status: aborted` and asserts the gate degrades.

**Depends on:** nothing.

---

**Notes**
Same class as DG-021 and DG-023: a status computed from a proxy rather than from evidence the
producer already emits. The evidence field exists in every case; it just is not read.
