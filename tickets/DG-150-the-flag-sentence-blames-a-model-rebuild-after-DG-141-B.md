# DG-150 — After DG-141 (B), the morning flag's sentence still blames a model rebuild for a change that is a player's details moving

**Layer:** 6 · **State:** todo · **Lane:** Davids-MacBook-Pro-48631 · **DG 3.0** · **frontend copy · small**
**Source:** Fred (`davidleess-eb [d4e70e]`), 2026-09-04 ~08:2x ET, while landing DG-141 under David's ruling "B";
verified by Greg at `frontend/src/lib/copy.ts:491` before filing. Filed 09-04 08:2x ET by Greg. Assigned to Bob (idle;
frontend lane).

**Problem:** `vintage_changed_no_score_delta` reads *"Our projections were rebuilt on a newer model run, and none of
the players we could compare moved."* Under the OLD formula that was true: the flag could only trip when the
provenance hash moved, and a microsecond timestamp made that every morning. **After DG-141's B change the flag trips
only when a real row moved** — and a row moves when a player's TEAM, ROSTER STATUS or AGE changes, with the model
files provably unchanged. So the sentence will confidently name a model rebuild that did not happen. The one word
that has to survive is that something about a PLAYER changed, not about our models.

**How we know:** `frontend/src/lib/copy.ts:490-491` (read 2026-09-04 08:2x ET); pinned by `copy.test.ts:42`. Fred's
measurement on live rows: with the B change, swapping yesterday's Sleeper player-list hash in leaves the number
identical, and bumping the governance version still trips it.

**Done looks like:** the sentence names a player-details change rather than a model rebuild, in David's language,
without claiming which player or which field unless the producer carries it (READ THE PRODUCER — `daily_diff.py`
around the `vintage_changed_no_score_delta` emit — and say only what it entails). `copy.test.ts:42`'s regex moves with
it, red first. `npm run gate` green. **The wording is David's call**; put a candidate to him through Greg before
landing, and do not ship a sentence that asserts a cause the payload cannot support.

**Anti-scope:** no producer change; no change to the ambiguous-vintage sentence beside it; nothing under `.oa3`.

**Depends on:** DG-141 (David ruled B 09-04 ~08:10; landing 09-04 morning, NOT live until the next pull).
**Do not land before DG-141 is live** — until then the old sentence is still the true one.

---

**Notes**
