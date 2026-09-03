# DG-142 — The roster page says three positions "passed accuracy checks" that were never run on the models now serving

**Layer:** 2 · **State:** open · **Lane:** unclaimed · **DG 3.0** · **product truth / trust surface · small**
**Source:** third-party review ("Bob") 2026-09-03, verified end-to-end by Tower's agent on the live API the
same morning. Every element of the claim reproduced.

**Problem:** `app/api/routes/roster_audit_models.py:69` compares `result.model_version != manifest[pos]`.
Both sides are the generic string `"engine_b_v2"` on all four positions, so **the staleness branch is
unreachable by construction** — the badge cannot notice that the binary changed. Live: `/api/roster/audit`
returns `model_status_by_position = {QB: PROVISIONAL, RB: VALIDATED, TE: VALIDATED, WR: VALIDATED}` with no
`trust_status_stale` caveat and `status: "active"`.

All four `backtest_result_*.json` are `run_date 2026-05-31`; `v2_manifest.json` and all four served `.pkl`
files are mtime **2026-08-31 16:44:58**. Running `check_served_alignment` for all four positions returns
**aligned=False on 4 of 4** — "the deployed model has been replaced since these figures were measured".
Measured hash pairs (published artifact vs sha256 of the served .pkl in run `20260831T204458Z`):
QB `d7acb680…`/`fbb3617b…` · RB `5507e37f…`/`03f67f7c…` · WR `3b83bbf9…`/`e1cbb125…` · TE `e2ca15ed…`/`84f05d61…`.

**What David sees today** (`RosterAuditHeader.tsx:62` + `copy.ts:951-955`): "RB passed its accuracy checks",
same for TE and WR; QB reads "passed the safety checks, but not every season we tested confirmed it".
**Nothing on that surface says the numbers describe a replaced model.**

**The product contradicts itself on the same screen.** All four `/api/trust-surface/{POS}` responses already
return `describes_deployed_model=false` with "These accuracy numbers were measured on an earlier version of
this model. The version answering today has been retrained since." (`trust_surface.py:25-28`), rendered at
`TrustStrip.tsx:101-102`.

**Fix shape:** the correct guard already exists — `src/dynasty_genius/eval/served_model_alignment.py:51`
`check_served_alignment` reads `v2_manifest.json`, sha256s the deployed `.pkl` bytes (`:48`) and compares to
the artifact's recorded `model_artifact_hash` (`:108`), failing closed. It is wired into exactly ONE of the two
consumers: `trust_surface.py:130` (guarded) and `roster_audit_models.py:60-61` (unguarded). Wire it into the
second.

**⚠ EXPECTED AND INTENDED CONSEQUENCE — David authorised this knowing it:** all four positions then fail closed
to EXPERIMENTAL, rendering as "not proven" (`copy.ts:954`), and a non-empty `trust_caveats` forces
`status="degraded"` (`roster_audit_models.py:335-336`), which renders "Heads up: this roster read came back
degraded — treat the numbers below as provisional." (`RosterAuditHeader.tsx:38-40`). **The fix makes the page
more truthful and less reassuring at the same time.** It stays that way until the backtests are re-run against
the `20260831T204458Z` bundles — which is the follow-on work, same day.

**⏱ SEQUENCING — David's instruction: land AFTER his ~09:15 morning read, not before.**

**Anti-scope:** do not "fix" it by regenerating model cards or by loosening the guard; do not touch
`trust_surface.py`, which is already correct. Bob's count of "three VALIDATED badges" UNDERCOUNTS by one —
QB's PROVISIONAL is unjustified by the same defect. Scope the fix to the comparison, not to the word VALIDATED.

**Verify:** `/api/roster/audit` reports every position as not-proven with a stale-trust caveat while the
backtests predate the served bundles; after a genuine re-run against `20260831T204458Z`, the badges return
to their earned states without any change to this code.
