# DG-135 — `frontend/openapi.json` does not declare the 503 that `/api/engine-b/scores` and `/api/roster/audit` now return

**Layer:** 3 · **State:** open · **Lane:** — · **DG 3.0** · **API contract / frontend · small, mechanical**
**Source:** DG-133 follow-up (its LANDED section, 2026-09-01); ticketed 2026-09-02 06:05 by Tower.

**Problem:** DG-133 (`f8995d3d`) made two routes answer
`503 {"error": "engine_b_dependency_unavailable" | "roster_dependency_unavailable", "message": <bare token>}`
when the inference partition cannot be selected (`app/api/routes/engine_b.py`, `app/api/routes/roster.py`).
`frontend/openapi.json` still declares only `200` for `/api/engine-b/scores` GET and
`/api/roster/audit` GET (verified 2026-09-02: `/api/roster/capacity` already declares `503`; the
other two do not). The generated client therefore has no typed shape for the failure the server
now sends, and the frontend's fail-closed rendering of it is untested against the contract.

**Fix shape:** regenerate, never hand-edit — `npm --prefix frontend run openapi-gen` from a
clean worktree at or after `f8995d3d`, commit the regenerated file ALONE, and diff it: the only
change must be the two new `503` entries (and their shared error schema if one is emitted).
See [[reference_openapi_regen_trap]] — a dirty working copy of this file has silently REVERTED
landed commits before; the diff review is the whole ticket.

**Anti-scope:** no route change, no frontend copy change, no other openapi drift; if the regen
produces anything beyond the two 503s, STOP and report what else moved — that is a different
ticket's landing being exposed, not this one's to absorb.

**Verify:** the openapi drift test passes; `git diff --stat` on the landing commit touches
`frontend/openapi.json` only; both routes list `200` and `503`.
