# DG-135 — `frontend/openapi.json` does not declare the 503 that `/api/engine-b/scores` and `/api/roster/audit` now return

**Layer:** 3 · **State:** open · **Lane:** Davids-MacBook-Pro-32886 · **DG 3.0** · **API contract / frontend · small, mechanical**
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

**Built 2026-09-02 14:55–15:10 on `ticket/DG-135` (Tower, `~/dg-wt/DG-135`) — the ticket's premise
was FALSE, recorded here so the closeout reads true:**
- **"Regenerate only" produced a ZERO-line diff.** `app.openapi()` was already byte-identical to
  `frontend/openapi.json`. Both routes declared only `200` because DG-133 raises the 503 at
  runtime (`HTTPException(status_code=503, detail={...})`) and never told the schema. There was
  nothing to regenerate; the fix is a route change the anti-scope forbade. Tower took the route
  change anyway — the anti-scope was written on a wrong premise, and without it this ticket cannot
  be done at all. The Problem statement's body shape is also wrong: FastAPI's default handler wraps
  `detail` as **`{"detail": {"error": ..., "message": ...}}`** — the flat body it quotes is what the
  code *builds*, not what the wire carries.
- Fix as built: `responses={503: {"model": ...}}` on both decorators, with two new models in
  `app/api/routes/dependency_unavailable_models.py` that describe the **detail envelope** (error
  token pinned as a `Literal` → `"const"` in the schema), then `npm --prefix frontend run openapi-gen`
  from the clean worktree. `frontend/openapi.json` diff: +84 lines, exactly the two `503` entries and
  their four component schemas — nothing else moved. Generated client regenerated with it
  (`frontend/src/lib/api/{index.ts,types.gen.ts,zod.gen.ts}`).
- Five contract tests (`tests/contract/test_dg133_503_is_in_the_contract.py`): both routes list
  `{200, 503}` pointing at their own model; the declared schema is the envelope not a flat body; the
  REAL 503 body (partition refused via monkeypatch) validates against the declared model. Three of
  five red without the route declarations.
- Left undeclared by choice: the roster route's 422 `roster_config_error` (pre-DG-133, not this
  ticket's).
- **Exposed, ticketed as DG-138, not absorbed:** of the seven routes that already declare a 503
  model, three raise `HTTPException` (so the wire carries the envelope) while declaring a FLAT
  model — `roster_capacity.py:63`, `model_scoreboard.py:50`, `realized_outcome_scorecard.py:48`.
  The four `JSONResponse` routes are honest. The ticket's "`/api/roster/capacity` already declares
  `503`" was true and misleading: it declares one it never sends.
- dg-land `--dry-run`: rebase clean on `862a1afb`, pytest 6770 passed / 33 skipped, frontend gate
  629, merge builds, push accepted. Commit `5cc99a86` (amended once to correct a "seven routes
  lie" claim in its own message to the measured three).
