# DG-128 — Rank everyone: the 8-game gate refuses 115 players we already have history for

**Layer:** 3 · **State:** todo · **Lane:** Davids-MacBook-Pro-77417 · **DG 3.0** · **backend / model · CHANGES PUBLISHED VALUES**
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

---

## David's ruling 2026-09-01 (verbatim) — the constraint this build runs under

> Option 1 — coverage fix, no slot. Rank-everyone is a standing product ruling, not an accuracy
> claim. Two conditions. First, build the taper to a pre-committed form and don't tune it against
> the holdout. If you find yourself comparing candidate priors to see which ranks better, stop —
> that's a slot and it needs registering. Second, the band ships with the number. There's no width
> field anywhere today, so building one is part of this work, not a follow-up. A prior-dominated
> estimate must not render with the same authority as a measured one. Stop and come back to me if
> already-ranked players' values or percentiles move. I expect they will, since percentiles are
> computed over the ranked population — I want to see the size of it before it ships. I'll pick the
> five separately, not tonight.

And, on the feature table: *"wait for tomorrow morning"* — the 09:00 chain publishes it with
DG-127's lags; nobody regenerates it by hand.

## Pre-committed form — written 2026-09-01 23:55Z, BEFORE any band was computed on a real player

**The taper is the Phase 15 form as it already stands. Nothing about it changes.** Pure Engine B
at `games_t >= 8`; `w_B = n/(n+k_pos)` blend for `1 <= n < 8` with `DVS_BLEND_K` untouched;
Engine A alone at n=0. The reason the blend has never fired is not the form — it is that the
serving path never supplied the Engine A inputs (`pick`, `round`, `age`). The coverage fix is to
supply them. Extending the blend past 8 would move every measured player's value and is a
modelling change; it is not done here.

**Three things must be true for a blank to fill, not one.** (a) Engine A inputs reach the assembler
so the blend can fire. (b) The blend's B component pays the availability hurdle — it did not
(`e6e73a03` fixed it; the pure-B branch had since `ee57d802`). (c) The roster index stops dropping
the rows the blend produces — `app/services/roster_auditor.py:151-152` admits only `ENGINE_A` /
`ENGINE_B` and `:189-232` would relabel a blended veteran as an Engine A rookie. Land (a) without
(c) and the gate looks fixed while David's three blanks stay blank. Verified 2026-09-01: served
`dvs_engine` is None 11,758 / B 388 / A 80 — the drop is latent, never observed, and goes live the
moment (a) lands.

**Engine A input for a veteran is his DRAFT-year age, never his current age.** `score_prospect`
was trained on prospects aged ~20–24 (WR `feature_means.age = 22.13`, WR age coefficient −0.81/yr);
feeding a 27-year-old's current age extrapolates the rookie model to an input it never saw (one WR
fixture at games_t=4: the blend served 63.8 with current age 27 and 81.5 with draft age 22). Draft capital comes from nflverse draft picks keyed on gsis; undrafted
stays undrafted (no Engine A prior, no blend — those players are the ticket's second half, not
this one). Never impute a pick.

**The band.** Two new fields on the PVO and every surface that carries the score:
`dvs_band_low`, `dvs_band_high` — DVS units, clamped to [0, 100] like the score, null wherever
the score is null. The basis marker is `dvs_engine` (already served as `engine_path`
`ENGINE_B` / `BLEND_AB` / `ENGINE_A`); no new field.

Half-widths, in DVS points, from the SERVED models' own published holdout error — nothing fitted,
nothing chosen by looking at a ranking:

    sigma_B[pos] = rmse(validation_report_{pos}.json, run 20260831T204458Z, metrics_v2) / ENGINE_B_P90_PPG[pos] * 100
    sigma_A[pos] = rmse({POS}_metadata.json, run 20260502T153931Z, metrics)             / ENGINE_A_P90_PPG[pos] * 100

                 QB      RB      WR      TE
    sigma_B     22.4    22.8    20.0    23.6      (1 holdout RMSE of E[points|plays], 2022-23 holdout)
    sigma_A     40.0    20.4    32.4    23.6      (1 holdout RMSE of y24_ppg, 2021 holdout, 10-35 rows)

    measured  (dvs_engine B,     n >= 8):   DVS ± sigma_B
    prior     (dvs_engine A,     no B):     DVS ± sigma_A
    blended   (dvs_engine blend, 1<=n<8):   DVS ± sqrt( sigma_B² + ((1-w_B) · (sigma_A + |DVS_A − DVS_B|))² )

where `DVS_A`, `DVS_B` are the two components exactly as they enter the blend (B already
hurdle-adjusted and clamped). Read: the measured model's error is always carried; on top of it,
the share of the prior's error and of the two engines' disagreement that the sample has not yet
resolved, combined root-sum-square as independent error terms conventionally are.

Properties, by construction rather than by inspection: the blended band is strictly wider than
the measured band for the same position (by the second term, > 0 whenever w_B < 1); it narrows
monotonically as n grows (d/dw < 0); it tends to the measured band as w_B → 1. The width is a
1-RMSE band — roughly a 68% interval if errors were normal — and it is NOT scaled by P(plays),
which leaves it wider than the arithmetic would give (conservative; P carries its own model error
that this absorbs in part). It says plainly that a measured Engine B player sits at ±20–23 points
of a 100-point scale: that is the served model's published error, and the band's job is to show
it, not to shrink it.

The only cliff left is the one the Phase 15 form already has at n=8 (the point estimate jumps
from the w_B=7/12 blend to pure B; `test_bayesian_bridge_monotonicity` bounds it at 20). The band
narrows across the same boundary by `sqrt(sigma_B² + (5/12·(sigma_A+|A−B|))²) − sigma_B`. Stated,
not smoothed: smoothing it means changing the taper, which is the slot David did not open.

**Rule for this section:** if a measured number makes any line above look wrong, the line is
amended IN A NEW DATED SECTION with the number that prompted it — never edited in place, and never
by trying a second form to see which reads better.

## Measurement plan (what David sees before anything ships)

Baseline vs DG-128 on the SAME input, same day, same partition. Both runs use a pre-filter to the
latest feature season per player because production's `training_eligible == False` partition
carries 1,143 rows / 29 duplicated players and trips `engine_b_prediction_conflict` at
`build_universe_pvo_batch.py:285` (the 09:00 / 11:30 / 14:00 refreshes all exit 1 on it today;
no fix is written or owned as of 23:50Z). The writeup will say so: the baseline is produced under
a pre-filter production does not have. Per-player diff, joined on `sleeper_player_id`: DVS, xVAR,
both percentiles, `engine_path` transitions, entered/exited-ranked, band widths by basis.
Expected: ranked population 468 → ~583; every position percentile moves because the denominator
does. That size is what he asked to see.

Known before running: the fix fills Garrett Wilson and Braelon Allen; it does not fill Tank Dell,
who has no 2025 feature row at all (`feature_assembly.py:177` floors at 4 games before the lag
join) — a censoring question for the second half. Bo Melton is unscored because the crosswalk
carries him as CB, not because of the gate.
