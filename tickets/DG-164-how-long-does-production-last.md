# DG-164 — How long does production last? The survival curve, measured

**Layer:** 3 · **State:** MEASURED — design + evidence, **no feature built** · **Lane:** Bob · **DG 3.0** · **dynasty asset number**
**Source:** David's dynasty-asset ruling 2026-09-05, relayed by Greg. The number is *what a player produces × how
long that lasts × how scarce that tier is.* This ticket is the middle term — the one the product has never had in
any form. Measured 2026-09-05 by Bob. **No code changed, nothing fitted to market price.**

---

## 0. FEASIBILITY GATE — asked first, and it passes decisively

Greg's instruction was to establish the panel reaches far enough before building on it, and to report it as the
answer if it does not. It does.

| | |
|---|---:|
| panel | **1999–2025**, 27 seasons, from `nflreadpy.load_player_stats` |
| player-seasons (QB/RB/WR/TE, REG) | **14,776** |
| age coverage (from roster `birth_date`) | **100%** |
| startable player-seasons | 3,348 |
| **cohort-year observations** | **14,880** across **925 distinct players** |
| cohorts with a full 5-year follow-up | **1999–2020** (22 cohort years) |
| observations at the hardest horizon (k=5) | **2,728** |

**Contrast with DG-162's ceiling.** The per-position feature models are fit on 264–910 rows and that is why more
features cannot help them. Survival is not built that way: every player-season in the panel is an observation of
*"was he still producing N years later"*, pooled across positions with position as a term. **2,728 observations at
k=5 against 264 for the QB model.** Fred's instinct was right — this is the one question in the product where the
data is genuinely sufficient, and it should be sourced from the panel, never from the model tables.

## 1. THE DEFINITIONS — each stated so it can be argued with

**STARTABLE** = finished in the **top N at his position by total regular-season PPR points**, with
N = **QB 25 / RB 33 / WR 53 / TE 13** (`ENGINE_B_VAR_THRESHOLDS` — the 12-team superflex full-PPR lineup
arithmetic David ruled as an order statistic, 08-31 ruling 3).

- **Not circular:** it references no model output, only the finish table. ✅ trap 2
- **Not the availability event:** deliberately not the ≥4-games label (77% base rate) that DG-163 measured as too
  coarse to carry a dynasty horizon. ✅ trap 2
- **Total points, not PPG:** a dynasty asset has to be available *and* good. The rate-conditional version is
  measured separately in §3 so the exit and the decline never get confused. ✅ trap 3
- ⚠ **REGULAR SEASON ONLY, and this differs in scope from DG-024.** David's "all games, postseason included" ruling
  governs Engine B's `ppg_t`, a model *feature*. This is a different quantity — *did he finish as a startable
  fantasy asset* — and fantasy leagues play the regular season. **Flagged for David rather than assumed.**

**COHORT** = everyone startable in season *s*, followed forward 1–5 years. **A player absent from the panel in
*s+k* is counted NOT startable, never dropped.** That is the guard against the 638-deleted-seasons defect that
created the original age bias. Years beyond 2025 are *excluded as unobservable*, never scored as a failure. ✅ trap 1

**NO MARKET PRICE ANYWHERE.** No valuation, ADP, KTC or FantasyCalc field was read. Nothing is tuned toward a known
answer. ✅ trap 4

## 2. THE EXIT CURVE — P(still startable in year k)

Pooled baseline across all positions and ages: **62.7% → 51.4% → 41.8% → 33.9% → 26.6%.** A startable player has
roughly a **one-in-four** chance of still being startable in five years. Split by position and age at the cohort
season, as **expected startable seasons in the next five** (the sum of the survival curve — the natural scalar for
"how long does it last"), cluster-bootstrapped on player, 90% intervals:

| age at t | QB | RB | WR | TE |
|---|---:|---:|---:|---:|
| ≤23 | 2.81 [2.36, 3.27] | 2.73 [2.38, 3.07] | **3.39 [3.12, 3.66]** | 2.55 [1.98, 3.12] |
| 24–25 | 2.88 [2.49, 3.24] | 2.23 [2.02, 2.46] | 2.67 [2.45, 2.88] | 2.05 [1.65, 2.41] |
| 26–27 | **3.21 [2.84, 3.54]** | 1.83 [1.58, 2.07] | 2.19 [1.96, 2.41] | 1.59 [1.20, 1.98] |
| 28–29 | 2.73 [2.32, 3.10] | 1.49 [1.23, 1.75] | 2.15 [1.88, 2.39] | 1.69 [1.17, 2.19] |
| 30–31 | 2.72 [2.35, 3.08] | 1.19 [0.92, 1.47] | 1.96 [1.70, 2.20] | 1.65 [1.10, 2.23] |
| 32+ | 2.08 [1.65, 2.42] | **0.50 [0.26, 0.70]** | 1.25 [1.04, 1.42] | 1.63 [1.01, 2.05] |

**Position is not a modifier on the age curve — it is a different curve.** A QB at 30–31 (2.72) has *more*
remaining horizon than an RB at 24–25 (2.23). A 32+ RB has **half a season** left, with a 5-year survival of
**0%** observed. The product currently expresses all of this through a single age coefficient on this season's
projection — and DG-162 measured that `aging_curve_value` is inert in all four served models anyway.

## 3. THE DECLINE IS NOT THE EXIT — and the decline is nearly flat

Of the players who *remain* startable, mean PPG as a share of their own cohort-season PPG:

| | y1 | y3 | y5 |
|---|---:|---:|---:|
| QB 30–31 | 100% | 103% | 103% |
| RB 30–31 | 99% | 89% | 74% |
| WR 30–31 | 101% | 91% | 89% |
| WR ≤23 | 116% | 116% | 114% |

**Conditional on still being startable, players produce at 74–117% of their old rate at every age.** Ageing in
fantasy football is overwhelmingly an **exit** process, not a fading process. The asset does not gently decay; it
works and then it stops. **This is the single most product-shaping thing in the ticket** — a value term built as
"multiply this year's points by a decay factor" would model the wrong mechanism entirely. The right shape is
*this year's production × the probability he is still there*, not *× a shrinking fraction of what he does*.

## 4. ELITE STATUS BUYS HORIZON — and it is worth years of age

Tier = finish rank as a fraction of the positional bar (elite = top 20% of the startable pool). Cells with n < 12
suppressed rather than reported:

| pos | age | elite (top 20%) | mid | fringe |
|---|---|---:|---:|---:|
| WR | 24–25 | **3.84** (n=41) | 3.12 (n=86) | 2.09 (n=146) |
| WR | 28–29 | **3.77** (n=40) | 2.44 (n=72) | 1.12 (n=99) |
| WR | 32+ | 2.24 (n=23) | 1.56 (n=38) | 0.69 (n=65) |
| RB | ≤23 | **4.39** (n=18) | 3.25 (n=32) | 1.82 (n=49) |
| RB | 26–27 | 2.56 (n=40) | 2.53 (n=55) | 1.04 (n=92) |
| QB | 28–29 | 3.77 (n=19) | 3.49 (n=26) | 2.02 (n=50) |
| QB | 32+ | 2.82 (n=15) | 2.74 (n=28) | 1.35 (n=49) |

**An elite 28–29 WR (3.77) outlasts a fringe 24–25 WR (2.09) by more than a season and a half.** Being good today
is worth more remaining horizon than being four years younger. There is no tier × age interaction anywhere in the
product today.

## 5. DAVID'S PROBE — both of his named complaints are resolved by this term alone

Using each player's **actual 2025 finish tier**, not the band average:

| player | pos | age | 2025 finish | tier | **E[startable seasons]** | 90% CI | n |
|---|---|---:|---|---|---:|---|---:|
| Jahmyr Gibbs | RB | 24 | RB3 | elite | **3.33** | [2.96, 3.70] | 37 |
| Bijan Robinson | RB | 24 | RB2 | elite | **3.33** | [2.99, 3.71] | 37 |
| Josh Allen | QB | 30 | QB1 | elite | **3.12** | [2.38, 3.78] | 15 |
| Breece Hall | RB | 25 | RB19 | fringe | 1.66 | [1.36, 1.94] | 110 |
| Christian McCaffrey | RB | 30 | RB1 | elite | **1.48** | [0.93, 2.00] | 9 |
| Derrick Henry | RB | 32 | RB8 | mid | **0.67** | [0.25, 1.00] | 7 |

- **McCaffrey, whom Fred showed survives every scarcity treatment at #2, falls decisively** — 1.48 against 3.33 for
  the two 24-year-olds. Two and a quarter times less remaining production. That is the defect David named, and the
  horizon term fixes it without touching scarcity.
- **Henry (0.67) drops below Breece Hall (1.66)** — David's other named complaint, also resolved.
- ⚠ **The horizon term does NOT put Allen first, and it cannot.** 3.12 [2.38, 3.78] against Gibbs 3.33
  [2.96, 3.70] — the intervals overlap almost completely, and Allen's cell is n=15. **This term separates all three
  from McCaffrey decisively and cannot separate them from each other.** If Allen belongs first it will be because a
  superflex QB1 season is worth more than an RB1 season — Fred's per-season value and scarcity terms, not mine.
  Anyone reading my table as a ranking is misreading it: **E[seasons] is a duration, not a value, and durations are
  not comparable across positions.**

## 6. WHAT IS KNOWABLE VS WHAT WE WOULD BE INVENTING

**Measured, and I would defend it:** the exit curves by position × age (every cell n ≥ 33 except TE); the flatness
of the decline; the tier effect for QB/RB/WR; the pooled baseline; David's three probe players against McCaffrey
and Henry.

**Thin — reported with n, do not build on it:** all of **TE** (most cells n = 4–20; TE's apparent flatness with age
is very likely small-sample, and 32+ TEs surviving *better* than 26–27 TEs is not credible as football);
**RB elite at 30–31 (n=9) and 32+ (n=4)**; **QB ≤23 elite (n=6)**; McCaffrey's own cell is n=9 with a CI spanning
0.93–2.00. The ordering against Gibbs survives the whole interval, but the point estimate should not be quoted alone.

**Would be inventing:**
- **Anything past 5 years.** The panel supports 5. A 10-year horizon would be extrapolation.
- **Any individual deviation from his cell.** This says what happens to *elite 24-year-old RBs*, not what happens
  to Bijan. DG-163 already measured that injury history does not predict the 2-year availability event; whether it
  predicts *this* one is untested.
- **Causality.** Elite players may last because they are good, or because teams keep handing them opportunity.
  The curve is descriptive and must be described that way.
- **A parametric survival model.** Cell rates are what the data supports today. Fitting Cox or Weibull would smooth
  the thin cells — and smoothing is exactly where invention enters. If a fitted form is wanted, the elite/fringe
  gap and the QB/RB divergence are the two effects it must reproduce or be rejected.

⚠ **The stop rule, taken from Fred verbatim:** if any term starts needing a free parameter aimed at a known answer,
that is the signal to stop. Nothing here has one. The cells are counts.

---

## Reproduce

    cd ~/dynasty-genius-product
    SP=/private/tmp/claude-501/-Users-davidleess/<session>/scratchpad
    .venv/bin/python $SP/panel.py      # 1999-2025 panel, cached
    .venv/bin/python $SP/survival.py   # cohorts + forward follow-up
    .venv/bin/python $SP/curves.py     # exit and decline curves
    .venv/bin/python $SP/tier.py       # tier interaction + David's probe
