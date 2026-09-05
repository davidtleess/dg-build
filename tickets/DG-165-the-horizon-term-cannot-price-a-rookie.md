# DG-165 — The horizon term cannot price a rookie, and rookies are the dynasty asset class

**Layer:** 3 · **State:** FILED — design decision needed, **not a lane's to make** · **Lane:** — · **DG 3.0** · **dynasty asset number**
**Source:** found 2026-09-05 by Bob while checking top-30 coverage for DG-164; confirmed and escalated by Greg and
Fred the same morning. **No code changed.**

---

## THE GAP

DG-164's survival term keys every player on **(position, age band, margin decile)**, where margin is his production
relative to the replacement bar. **A rookie has no NFL season, therefore no production, therefore no margin,
therefore no cell — at any sample size.** This is not a thin-cell problem and no threshold fixes it.

- **All 80 rookies on the board are unpriceable by the panel** (Fred's count).
- **Jeremiyah Love is inside the top 20** and cannot be given a horizon.
- Also affected in the top 30 by DVS: **Harold Fannin, Jadarian Price.**

⛔ **A dynasty number that cannot price a rookie is missing the thing it exists for.** Rookie picks and first-year
players are the central traded asset in dynasty leagues.

## WHY IT WAS NOT CAUGHT EARLIER — a harness that tested the band, not the player

Fred's first coverage check reported all twenty of the top 20 covered. It keyed on **(position, age band)** and
found those bands well populated. **But a rookie fails on the margin, not the band** — his age band is fine and he
still has no cell. *"The check tested the apparatus, not the subject"* (his words), the same shape as
[[feedback_a_null_needs_a_sample_that_spans_the_effect]].

⚠ **The open question that matters more than the count: are the rookies BLANK on the asset board, or ABSENT from
it?** A board that silently omits them is worse than one that says "we cannot tell you yet", and it is the standing
defect shape ([[feedback_the_failure_path_returns_the_success_signal]]). Confirm before the top 20 ships.

## THREE WAYS OUT — none of them a lane's to pick

1. **Blank with a reason.** Honest, cheap, and leaves the most-traded assets with no number on a dynasty board.
2. **Borrow a horizon from draft capital**, the way Engine A already scores rookies. We *have* a rookie estimator.
   ⚠ It is **a different estimator with a different error**, and mixing two estimators inside one product without
   marking which produced each number is how a board becomes unauditable. Fred's instinct, and mine.
3. **Exclude rookies and say so loudly.** Defensible only if said loudly; silently is option 2's failure mode.

**Feasibility note, untested:** a rookie survival curve would need a college-to-NFL panel keyed on draft capital
rather than prior NFL production. DG-164's feasibility gate passed because every player-season is an observation;
the rookie equivalent would need the same gate run before anyone commits to option 2.

## WHAT THIS TICKET IS NOT

- **Not a defect in DG-164.** The survival cells are correct for the players they cover; the term simply has no
  input for a player with no season. Stated in DG-164 §5n.
- **Not blocked on the bar question.** Rookies are unpriceable under every candidate bar.
- **Not solvable by lowering `MIN_N`.** There is no cell to populate.
