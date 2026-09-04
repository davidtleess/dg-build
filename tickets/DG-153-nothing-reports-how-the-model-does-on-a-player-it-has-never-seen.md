# DG-153 — Nothing reports how the model does on a player it has never seen

**Layer:** 3 · **State:** todo · **Lane:** Davids-MacBook-Pro-48631 · **DG 3.0** · **model honesty · medium**
**Source:** Bob (`davidleess-08 [b202b7]`), 2026-09-04 ~13:5x ET, found while measuring DG-027/DG-026 and filed at
Greg's direction as its own ticket — it is a bigger finding than the split bug it fell out of and must not be buried
in that closeout. **Neither DG-027 nor DG-026 names it.**

**Problem:** every published Engine B number is measured on a holdout that shares most of its players with training.
On the frame the fit actually sees, **65.6% of holdout players also appear in the training rows** (QB 75.0%, RB 63.0%,
WR 64.9%, TE 64.4%). So the reported skill is largely skill at RE-RATING a player the model has already met. Nothing
anywhere reports the other number — how it does on a player it is meeting for the first time — and that is the waiver
pickup and the rookie, the two cases David most needs help with.

**How we know (measured 2026-09-04, read-only, in `~/dg-wt/DG-027`; alphas reproduce the served run
`20260831T204458Z` exactly, so this is the shipped model, not an approximation).** Alpha selected by player-grouped
CV, scored on the 2022-23 holdout, then scored again on only those holdout rows whose `player_id` never appears in
the fitted training rows:

| pos | published r² | honest-split r² | **unseen-player r²** | rho published → unseen | unseen rows |
|-----|-------------:|----------------:|---------------------:|-----------------------:|------------:|
| QB  | +0.439 | +0.365 | **−0.158** | 0.696 → 0.416 | 18 |
| RB  | +0.593 | +0.600 | **+0.461** | 0.788 → 0.709 | 60 |
| WR  | +0.682 | +0.691 | **+0.612** | 0.806 → 0.764 | 88 |
| TE  | +0.641 | +0.597 | **+0.483** | 0.789 → 0.578 | 44 |

**A negative r² means that for a quarterback the model has never seen, it is worse than predicting the average.**
The published claim for QB is +0.439.

**⚠ CAVEATS THAT TRAVEL WITH THESE NUMBERS AND MUST NOT BE STRIPPED:** single split, point estimates, no repeats;
the unseen samples are small (QB 18 rows especially). **The DIRECTION is solid — every position drops, none
improves. The MAGNITUDES are rough.** Nobody should quote them as settled, including the lane that produced them.

**Done looks like:** whatever reports model skill also reports it on unseen players, or refuses to.
1. **"Unseen" is defined against the ACTUAL FITTED ROWS, never the CSV.** The fit drops rows (`training_eligible`
   is False on 1,143 of 3,384), so a player can sit in the file and still be unseen by the model. The definition
   belongs in the code and in the report, stated.
2. **The metric REFUSES rather than reports below a stated sample floor.** QB's n=18 is exactly the case that would
   otherwise ship a confident number; an honest surface says "too few unseen players to say" and names the count.
3. The figure appears wherever the position's skill is claimed, beside the pooled one, never instead of it — both
   are true of different questions.

**Anti-scope:** does not retrain, re-promote or change any served value; a promotion is David's word through
DG-058/059, which are unbuilt. Does not touch `XVAR_LAMBDA_ENGINE_B` (DG-092) or the coupled TE constants.

**Depends on:** nothing. Reads better after DG-027's grouped-CV fix lands, but stands alone.

---

**Notes**

- **Population definitions, because two lanes measured two different things and both were right.** Greg's
  spot-check said 53.5% holdout overlap; mine said 79-90%. Reconciled 2026-09-04 ~14:1x ET: his pooled figure
  counted `feature_season >= 2022`, which sweeps in the **505 unlabelled 2025 rows — the inference partition, not a
  holdout at all** (0 of them are `training_eligible`). Mine was measured on the RAW csv before the eligibility
  filter. **On the frame the fit sees, 2022-23 only, the answer is 65.6% pooled** and the per-position table above.
  My "79-90%" was the raw-csv number and is **retracted**; the r² measurements were always computed on the fitted
  frame and stand.
- **Same lesson on the repeated-row figure.** I reported 52-58% of training rows "share a player"; that was the
  SURPLUS count (a player with k rows contributes k−1). Greg counted every row belonging to a repeated player (k),
  which is the right statistic for a leak question — **85-90% per position on the fitted train rows.** Both numbers
  are true; his is the one that answers "how many rows can leak", and it is the one to quote.
