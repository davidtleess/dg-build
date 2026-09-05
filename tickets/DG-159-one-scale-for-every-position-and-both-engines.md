# DG-159 — One scale for every position AND both engines: the rookie engine stops flattering rookies

**Layer:** 3 · **State:** open · **Lane:** Davids-MacBook-Pro-69536 · **DG 3.0** · **model correctness / product truth · medium**
**Source:** David's rulings, in sequence and all verified at source:
* 2026-09-04 19:34:46Z (15:34 ET): *"we need to put the tight ends on the same kind of scale as the rest of the
  players … It can't have its own scale; it can have a calibration to the position, but it has to be on the same
  scale."*
* 2026-09-04 22:30:11Z (18:30 ET): *"take decision one now. then build before week 1"* — decision one shipped as
  DG-157; this is decision two.
* 2026-09-04 (ceiling): *"The ceiling should either be the absolute best player in the league or it should be
  something mathematically achievable because we believe it is a Hall of Fame level Dynasty asset or Contender
  asset. It shouldn't just be an unreachable or extremely reachable number."*
* 2026-09-05 00:02:06Z (20:02 ET): *"normalize the rookie rankings into the same scale as everyone else."*

**Problem:** the displayed 0-100 score divides by a per-position ceiling, and there are EIGHT of them — four per
engine. A 100 means 20.1 points a game for a veteran QB and 16.7 for a rookie one; 9.4 for a veteran TE and 9.1 for
a rookie. Same position, same football, different yardstick. Two consequences:
1. **Cross-position:** eight tight ends pile up at 100 because the whole startable TE range is 8.99→9.40 ppg.
2. **Cross-engine, and this is the one David has not seen:** the rookie engine divides by SMALLER numbers, so
   **rookies are flattered**. On his own roster Fernando Mendoza reads 85.1 — the highest number he owns — and on
   one scale reads 70.0, behind Jaxson Dart at 75.6. All four of his Engine-A players fall further than his
   Engine-B players do.

**What makes this small, verified rather than assumed (Fred, 2026-09-04):**
* Engine A **already follows the same guarded identity** Engine B is held to: `lambda[pos] == P90[pos]/P90['WR']`
  on its own anchor, all four matching to three decimals. (A review lens asserted it did not; it does.)
* **The two engines already AGREE on replacement in points per game** — QB 12.91/12.90, RB 7.29/7.28, WR 8.79/8.79,
  TE 8.99/8.99. They differ ONLY in the ceiling they divide by (anchor 12.7 vs 14.5), which is the entire 1.1417×
  cross-engine gap.

So this is not a reconciliation of two worldviews. One denominator makes every lambda 1.000, makes the two
replacement tables identical (because the underlying ppg already agree), and closes the gap. **Eight ceilings,
eight replacement tables and eight lambdas collapse to one ceiling, four replacements and one lambda.**

⛔ **THE DENOMINATOR VALUE IS DAVID'S AND IS NOT SETTLED.** Measured band that satisfies his ruling — exactly one
player at or just under 100, nobody else — is **D ∈ [19.70, 20.31]**. Below ~15.9 many players pin ("extremely
reachable"); at/above ~22.0 nobody can reach 90 ("unreachable"). ⚠ An excellence standard drawn from REAL achieved
production fails his own test: at D=29.45 (best achieved) nobody in the league scores above 68.9, because the
model's forecasts run ~30% below real outcomes (regression plus the availability discount). **Do not assume a value
in the preparation commit.**

**Shape, per Greg's sequencing ruling:**
1. **PREP — behaviour-neutral, lands early.** A single scale module whose derivations (ceiling → lambda, ceiling →
   replacement DVS, rmse → sigma) **reproduce today's shipped constants exactly** from today's per-position
   ceilings. Nothing wired, no served number changes. That reproduction IS the evidence the switch is correct.
2. **SWITCH — ONE atomic commit.** Every constant that moves and every test that moves with it, together. The
   coupled family cannot move in pieces and the change must revert in one commit.

⛔ **ON THE DG-092 GUARD — do not let it pass vacuously.** Under one denominator `lambda == P90[pos]/P90[anchor]`
becomes trivially 1.000, and *a guard that becomes trivially true has stopped guarding*. The replacement invariant
must be **stronger**: every lambda IS 1.000 and nothing can set it otherwise; **all four positions and BOTH engines
divide by the SAME named constant**; and a second denominator cannot be reintroduced. The commit must state what
the old test caught, what the new one catches, and why the new one is not weaker.

**Depends on:** DG-158 (Bob) must land FIRST — its four score-unit constants break quietly when the scale moves,
and they are defensive: dormant until scores actually move. This ticket's switch commit is what arms them.

**Anti-scope:** no model retrain or promotion (David's word, and DG-058/059 are unbuilt). No tuning a constant to
make a position look right (his 2026-08-31 ruling 5). No market anchor (his ruling 8: market price is never a model
input). The replacement RANKS are a separate open question (the RB threshold is ~10 ranks too deep) and are NOT in
this ticket.

**Verify:** the prep module reproduces all eight ceilings' derived constants exactly; after the switch every lambda
is 1.000, both engines read one constant, no test passes vacuously, and David's four Engine-A players move as
measured.
