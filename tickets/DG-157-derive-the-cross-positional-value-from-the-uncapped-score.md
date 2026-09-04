# DG-157 — Derive the cross-positional value from the UNCAPPED score, so seventeen players stop being priced at a ceiling

**Layer:** 3 · **State:** open · **Lane:** — · **DG 3.0** · **model correctness / cross-positional · small**
**Source:** David's ruling, 2026-09-04 22:30:11Z (18:30 ET), verbatim and in full: *"take decision one now. then
build before week 1"* — answering a two-decision brief in which decision one is this and decision two is the
single-denominator rescale (a separate ticket). Measured and verified by Fred (`davidleess-eb [d4e70e]`).

**Problem:** `pvo_assembler.py` clamps the displayed score to 100 and then derives the cross-positional value
**from the clamped number**. For a player above his position's ceiling that value is not his — it is the ceiling's.
Seventeen players are priced that way today (TE 8, RB 5, WR 4), and each position's clamped players all share one
identical cross-positional value: TE 2.85, RB 58.05, WR 39.40.

**What it costs:** the cross-positional value is what the product uses to compare players ACROSS positions — trade
math, cut ordering, the opportunity map. Trey McBride and Colston Loveland are both priced at 2.85 although the
model separates them by 52% of a point per game. Largest corrections available (upper bounds, see below):
Puka Nacua +40.0, Trey McBride +38.9, Christian McCaffrey +21.6.

**The fix:** compute the cross-positional value from `dvs_raw` — the uncapped score — instead of the clamped one.
That restores exactly the identity the contract already documents: `(ppg − replacement_ppg) × 100 / P90[anchor]`,
in which the position ceiling cancels. Nothing else changes.

**Verified before filing, on the live artifact (`captured_at` 2026-09-04T18:00:04Z, 502 Engine-B scored):**
* **Zero of 502 displayed scores move** — by construction, not by measurement: the displayed score is set on one
  line and the cross-positional value derived from it on another; this edits only the second.
* **Exactly 17 cross-positional values change**, and nobody else's — for an unclamped player the raw and clamped
  scores are the same number.
* **No constant moves**, so both DG-092 identities hold and the 14 coupled-constant contract tests in
  `tests/contract/test_phase15_xvar.py` pass untouched. Run, not assumed.

⚠ **THE CONSEQUENCE THAT MUST BE FINDABLE LATER, because the cards look identical.** The cross-positional value is
also a RANK. **103 of 502 players change place** — the 17 whose value moves, plus **86 they leapfrog whose own
value does not change at all.** That rank (`xvar_percentile_overall`) is a sort key for the roster cut ordering, so
cut advice can shift for players whose card is byte-identical. **David has been told this in his own terms before
he ruled.** It is the fix working, not a defect, but nobody should rediscover it as a mystery.

**Anti-scope:** no constant moves — not `ENGINE_B_P90_PPG`, not `ENGINE_B_REPLACEMENT_DVS`, not
`XVAR_LAMBDA_ENGINE_B` (DG-092 guards it). No displayed-score change. No model retrain or promotion. Not the
single-denominator rescale, which is decision two and its own ticket.

**Not blocked by, and does not block, the surface work.** The three coupled pieces Bob holds (the prospect-card
invariance gate, the hard threshold at 80, the uncapped what-changed model section) are all triggered by a
DISPLAYED-SCORE change. This ticket moves no displayed score, so it lands safely alone; they are coupled to
decision two.

**Verify:** on the live artifact, 0 displayed scores differ; exactly 17 cross-positional values differ; the 14
coupled-constant contract tests pass; and a clamped player's new value equals the uncapped identity.
