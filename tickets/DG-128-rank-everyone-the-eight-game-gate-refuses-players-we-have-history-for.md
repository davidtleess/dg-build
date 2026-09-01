# DG-128 — Rank everyone: the 8-game gate refuses 115 players we already have history for

**Layer:** 3 · **State:** todo · **Lane:** — · **DG 3.0** · **backend / model · CHANGES PUBLISHED VALUES**
**Source:** David's ruling 2026-08-31 — *"rank everyone, always; confidence is a WIDTH, never an
ABSENCE. Abstention as a product behaviour is over."* It is the ONLY one of his ranking rulings
still unsatisfied, and it is the question he originally asked.

**Problem:** `468` of `12,226` served players carry a `dynasty_value_score`. On the honest
denominator — skill position, NFL roster, Sleeper-active — **`498` of `954` have nothing.**
David's own roster shows three blanks. Unranked players are then silently counted as `0.0` in
team value (`team_value_matrix.py`) and dropped from trade math.

Measured on the 2025 inference set:

    2025 rows                                  505
    below the gate (games_t < 8)               115
      with a prior-season ppg IN THE SAME ROW   72
      with TWO prior seasons                    55
      genuinely thin                            43

**So 63% of the gap needs NO new data.** The designed rescue — a Bayesian A/B blend — has fired
**0 times in 866,861 rows across 67 days**, because it requires an Engine A prior needing
draft pick+round, which the active feature table does not carry.

**Approach (David's ruling constrains this):** replace the cliff with a taper. A shrinkage
weight `w = n/(n+k)` is defined at n=0 and has no branch to fail, so no constant can null a
score. Band WIDTH comes from the availability model already in the serving path (`ee57d802`).
DG-127 is the enabler — without a games lag the taper still cannot tell a four-year pro with
one short season from a true rookie.

**⚠ The residual after the cheap fix:** the 43 genuinely thin players plus rookies need a
prior. The `contracts` table (535,660 rows, ~1.71 GB/day, **zero readers**) carries
`draft_overall` for 5,018 players and guaranteed money at 100% fill — observable for a player
with zero snaps and for UDFAs where draft capital is null by construction. That is the second
half, not the first.

**⚠ Ranking everyone has a real cost, and it is the front end's problem to solve, not the
model's:** ~500 players will carry a number that is mostly PRIOR, sitting on a list next to a
number built from seventeen games. A wide band is honest; a wide band that looks like knowledge
is not.

**Honesty law:** never present a prior-dominated estimate with the same visual authority as a
measured one.
**Done:** every Sleeper-active NFL-rostered QB/RB/WR/TE resolves to a finite score AND a finite
band, asserted by a serving-time test; no `None` reaches `team_value_matrix`; band width is
demonstrably monotone in evidence.


---

## ⚠ ENABLER LANDED 2026-09-01 (DG-127) — read these three before designing the taper

1. **`games_t_minus_1` / `_minus_2` now exist, and they are LEFT-CENSORED AT 4 GAMES.**
   `feature_assembly.py:178` drops sub-`MIN_GAMES_THRESHOLD` player-seasons before the lag join, so
   a 1-3 game prior season is written NaN/`_available = False` — byte-identical to a true rookie.
   Min observed `games_t_minus_2` on real data is 5.0. **A taper keyed on absence will shrink an
   injured veteran toward the rookie prior**, which is the exact population this ticket exists to
   rescue. `n` for the shrinkage weight must not be read off availability flags alone.

2. **The imputer guard is set OPPOSITE in training and evaluation, and this ticket is where it
   detonates.** `scripts/train_engine_b.py` fits `SimpleImputer` at :207, :309 and :387 WITHOUT
   `keep_empty_features`; `src/dynasty_genius/eval/backtest_harness.py:489` fits it WITH. The moment
   the taper adds these lags to a per-position feature set, an all-NaN slice (a thin
   position-season, an early fold) silently narrows the matrix while the bundle keeps advertising
   the full feature list. The backtest and the trained model then disagree about the input set by
   construction. Fix the flag before consuming the columns.

3. **Adding them to `ENGINE_B_BASE_FEATURES` changes David's screen.** Measured: `feature_completeness`
   moves for 229 of 505 scored players, the displayed value changes for 227, and the caveat sentence
   renders the raw string "games t minus 1" because `frontend/src/lib/copy.ts` `INPUT_NAMES` has no
   entry for the lags. Two existing tests break closed. Budget the copy-dictionary entry and the
   contract-test updates as part of this ticket, not as a surprise.

Detail and commands: `docs/agent-ledger/2026-09-01.md`.
