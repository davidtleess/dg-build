# DG-158 — The four score-unit constants that break when the scale moves

**Layer:** 3 (+6) · **State:** todo · **Lane:** — · **DG 3.0** · **coupled with Fred's rescale · medium**
**Source:** Fred found three while scoping the tight-end rescale; Bob verified all three and found a fourth. Filed
09-04 ~18:4x ET by Bob. **David ruled the rescale ships before kickoff** (2026-09-04 22:30:11Z = 18:30 ET, verbatim):
*"take decision one now. then build before week 1"*.

**Problem:** the rescale replaces the per-position denominators with a single anchor (20.1), so every DISPLAYED score
moves by that position's factor — **Engine B: QB 1.0000, RB 0.7811, WR 0.7214, TE 0.4677; Engine A: QB 0.8308,
RB 0.7264, WR 0.6318, TE 0.4527.** Four constants elsewhere are expressed in SCORE UNITS and do not move with it.
Each is silently coupled to a scale nobody thought of them as depending on.

**How we know — measured 2026-09-04 on the served artifact (582 scored rows, 502 Engine B / 80 Engine A):**

1. **The counter-argument threshold collapses.** `decision_logic/counter_arguments.py:50` gates the MANDATORY
   top-asset counter-argument on `val > 80`. To still clear 80 a player must score TODAY: **80.0 QB-B, 102.4 RB-B,
   110.9 WR-B, 171.0 TE-B** (and 96.3 / 110.1 / 126.6 / 176.7 on Engine A). **Scores are clamped at 100, so it
   becomes IMPOSSIBLE at three of four positions on each engine.** Population 57 → 9, all quarterbacks.
   **41 players lose the mandatory argument with no risk flag to catch them**, three of them David's own:
   AJ Barner 90.0→42.1, Tucker Kraft 100.0→46.8, Fernando Mendoza 85.1→70.7. Constitution Rule 4 calls this
   counter-argument mandatory.
2. **The prospect boards stop rebuilding.** `scripts/refresh_prospect_cards.py:392` exits 1 when any DVS drifts more
   than `_DVS_INVARIANCE_TOLERANCE = 0.01` from baseline. **82 cards, 80 with a baseline, every one moves.** The gate
   is a CONTAMINATION detector — `enrich_te_prospects_cfbd_2026.py:12-13` exists to set a baseline so the refresh
   "passes the DVS-drift gate cleanly" — so it must be told about this movement, **never deleted**.
3. **The morning page reports the whole league as fallers.** `what_changed/daily_diff.py` caps the market section at
   `top_n = 25` and reports `total_movers_count`; **the model section returns `deltas` UNCAPPED.** Live right now it
   emits **387**; on rebase morning **582**, every one a faller, **263 of them rostered**. The cap matters on an
   ordinary morning too, not only on rebase day.
4. **The band becomes wider than the position it describes.** `models/dvs_band.py` stores `DVS_SIGMA_B` (QB 22.4,
   RB 22.8, WR 20.0, TE 23.6), `DVS_SIGMA_A` and `DVS_SIGMA_A_V3` in DVS points. The scale shrinks underneath them.
   Band width as a share of the whole best-to-worst range, today → after: QB 0.47× → 0.47×, RB 0.46× → 0.58×,
   WR 0.40× → 0.55×, **TE 0.47× → 1.01× — wider than the entire tight-end scale, at the one position the rescale
   exists to fix.** Off-screen since DG-144 but still served on the API and the PVO.

**⚠ Piece 4 fails LOUDLY, not silently, and that matters for sequencing.**
`tests/contract/test_dg128_dvs_band.py:134/144/154` recomputes every sigma as
`rmse / ENGINE_B_P90_PPG[pos] * 100` (and the Engine A equivalents), so moving the denominators **fails nine
assertions and blocks the land** rather than shipping a wrong band. **The one path where it could go quiet is a NEW
anchor constant introduced beside the old `ENGINE_B_P90_PPG` rather than replacing its values** — then the score uses
the new denominator while the band's test still passes against the old one. That specific hole is what this ticket
closes.

**Done looks like:** each of the four expressed relative to the scale, or told about the move, so **the NEXT rescale
needs no hand-edit** (Greg, 09-04: *"fix the class, not the instance"*). Reversible in one commit. Tests red first.

**Anti-scope / not mine, confirmed with Fred:** the single denominator, the lambdas moving to 1.000, and the
replacement-level constants are his core. `XVAR_LAMBDA_ENGINE_B` is DG-092-guarded and derives from the denominators
by the cancellation identity (`engine_b_contract.py:19`) — **not touched here.**

**Depends on:** nothing to build. **Sequencing agreed with Fred and ratified by Greg: THIS LANDS FIRST AND DORMANT,
his core lands LAST as the switch. Neither alone.** These four change nothing observable until scores move.

---

**Notes**
