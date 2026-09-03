# DG-143 — Lower the season-games gate from 8 to 4 so a player who missed most of a season still gets a number

**Layer:** 2 · **State:** open · **Lane:** unclaimed · **DG 3.0** · **product truth / coverage · medium**
**Source:** David's ruling 2026-09-03, in response to a written either/or. His answer was option A —
"drop the season threshold from 8 games to 4" — chosen over "only the 72 with a prior season on file"
and "leave them blank and explain why". Rationale in his words: *"the model is always making its
genuine estimate"*. This supersedes the coverage half of DG-130.

**Standing language ruling attached to this work, David 2026-09-03 verbatim: "no 'partial season' lang".**
He refused a proposed explanatory sentence for these players. **The number ships bare.** No caveat copy,
no hedge, no badge. This is consistent with his DG-128 ruling to let the number stand.

**Problem:** `ENGINE_B_MIN_GAMES_T = 8` (`src/dynasty_genius/models/engine_b_contract.py:143`) withholds the
0-100 value from any player with fewer than 8 games in the feature season. Live: **115 ENGINE_B rows carry a
null `dynasty_value_score`, 114 of them with `games_t` 4-7** (4:33, 5:29, 6:31, 7:21). Three sit on David's
own roster. The gate reads ONE season — `games_t` — so a player with a full career and one interrupted year
is refused, while the model has already produced a projection for him (all 115 carry `projection_2y`).

**What the evidence does and does NOT support (2026-09-03 measurement, recorded so nobody re-argues it):**
- The auditor's case for ungating — holdout RMSE 3.28 for 4-7-game rows vs 3.19 for 8+ — **does not survive**.
  The eligibility filter conditions on the outcome, so it measured only the 50.7% of that cohort who came
  back (vs 84.9% of the healthy). Scoring the served quantity honestly, ordering collapses: **Spearman 0.380
  vs 0.781**, 0 of 10,000 bootstrap draws overlapping. **Do not cite 3.28-vs-3.19 as justification.**
- **This lands on David's ruling, which does not need a holdout comparison.** The honest quality statement is
  "roughly half the ordering skill" — a fact for the record and for future work, NOT copy for the screen.
- The served value already applies a real discount: `P(plays) x E[points | plays]`. Measured availability on
  the live 2025 population: gated 4-7 cohort **mean 0.488** vs 8+ **mean 0.847**. The model is not blind to
  the absence; it prices it.

**Worked examples, computed 2026-09-03 through the real availability path (`score_rows`) — David has seen these:**
- **Garrett Wilson** (WR, 7 games, 14.2 ppg, 87% snaps): projection 11.23 ppg x P(plays) 0.885 = 9.94 ->
  **DVS 68.5** (undiscounted 77.5; the availability cut costs 9.0). Would slot ~WR37 of 199 scored WRs.
  The market has him WR16 / overall 46 — **the market rates him well above our model**, the opposite of the
  gun-shy story, and worth instrumenting rather than assuming.
- **Braelon Allen** (RB, 4 games, 3.8 ppg, 23% snaps): 4.89 x 0.657 = 3.21 -> **DVS 20.5**. Both a backup and
  genuinely hurt — snaps weeks 1-4 then a week-5 knee DNP and no further appearances (verified in
  `nflverse_injury_report` + `player_snap_count`).

**Fix shape:** `ENGINE_B_MIN_GAMES_T` 8 -> 4. Players at `games_t` 4-7 leave the dead-window branch and take
the PURE Engine B path (`pvo_assembler.py`), which already applies the hurdle. The precision-weighted blend
(`pvo_assembler.py:459-482`) keeps its `[1, gate)` window and continues to fire only where an Engine A prior
exists — **0 live rows today**, so the blend is not the mechanism here and must not be re-scoped to become one.

**⚠ MEASURE BEFORE LANDING — the population change is not confined to the 114:**
`xvar_percentile_overall` / `xvar_percentile_position` and `dvs_pct` are computed ACROSS THE SCORED
POPULATION. Adding 114 rows to 468 moves **every existing player's percentile**. Quantify the shift on
David's 27 and on the top 50 overall before landing, and report it to him — he must not discover a moved
percentile on a player he did not think was touched. Also re-check the ceiling population (`dvs_clamped`)
and the 8-TE-at-100 saturation, which this may worsen.

**Anti-scope:** do NOT touch `XVAR_LAMBDA_ENGINE_B`, `ENGINE_B_P90_PPG` or `ENGINE_B_REPLACEMENT_DVS` (a
coupled system, DG-092 guards it). Do not add explanatory copy (see the language ruling above). Do not widen
the blend. Do not change `training_eligible` or the 4-game pre-lag filter in `feature_assembly.py` — that is
separate work. Tank Dell is NOT reached by this ticket: he has no feature row and no `dg_player_id`.

**Verify:** null-DVS ENGINE_B rows 115 -> ~1; David's roster blanks 3 -> 1 (Dell only); Wilson 68.5,
Allen 20.5; the percentile shift on his 27 reported to him in writing before the land.
