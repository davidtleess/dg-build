# DG-160 — Show the reasoning behind replacement level, computed from his real lineup so a wrong derivation is visible

**Layer:** 3 (+6) · **State:** open · **Lane:** — · **DG 3.0** · **product truth / bug detector · small**
**Source:** the unbuilt SECOND HALF of David's 2026-08-31 ranking ruling 5, given directly by him:
*"REPLACEMENT LEVEL — let the derived number stand and show the reasoning. Compute it as an order statistic from
the real lineup structure. 'Replacement TE = the 12th-best TE, because your league starts 12.' Accept the answer
even if the best TE ranks below WR46. Do NOT tune constants until TEs look right."*
The order-statistic half exists in code (`ENGINE_B_VAR_THRESHOLDS`). **The "show the reasoning" half was never
built**, four days on.

**Problem:** his roster row detail prints *"Value over replacement: 2.85"* and nothing else. He cannot see what the
number is measured against — not the rank, not why that rank, not what it resolves to in points per game.

**Why this is worth doing NOW, and it is not cosmetic.** This ruling is a **bug detector**, and having it switched
off cost a wrong constant that survived to six days before kickoff. `ENGINE_B_VAR_THRESHOLDS['WR'] = 53` is
documented as *"12 × 3 = 36 + ~7 flex + buffer"* — built on a **third receiver slot his league does not have**
(the snapshot reads QB 1, RB 2, WR 2, TE 1, FLEX 2, SUPER_FLEX 1). Had the roster printed *"replacement receiver =
the 53rd-best receiver, because your league starts three"*, he would have said "I start two" in August. Build the
thing that would have caught the bug.

**Done looks like:** for each position, the derivation stated the way he stated it — the **rank**, the **reason
from his real starting slots**, and the **points per game it resolves to** — computed from the LIVE league snapshot
rather than from the comment above the constant, because the comment is the thing that was wrong.

⛔ **THE DETECTOR TEST, which this must pass to be worth landing:** *if the derivation were completely wrong, would
this look any different?* Printing "replacement = 8.79 ppg" fails that test — a wrong rank is invisible. Printing
"the 53rd-best receiver, because your league starts 3 WR plus flex" passes it, because the premise is on screen
next to a league that starts 2. **The surface must therefore carry the slot arithmetic, not just the result, and
must compare the shipped rank against the one his lineup implies and say when they disagree.**

⚠ **Where the flex share is an assumption rather than a lineup fact, SAY SO on screen.** The dedicated slots are
unambiguous (12 × QB1/RB2/WR2/TE1 = 84 starters); the 24 flex and superflex places are shared, and how they split
between positions is behavioural, not structural. Measuring it honestly is not currently possible — 52 daily
snapshots are the same lineups re-observed with two edits between them, i.e. **one observation of 21 filled flex
slots, not 1,091**, and pooling them would be pseudo-replication. That uncertainty is exactly what his ruling wants
visible rather than hidden behind a round number.

**Anti-scope, and hold to it.** **Change NO constant. Change NO number he already sees.** This is a derivation made
legible, not a correction — the receiver-threshold fix and the rescale are separate and both wait on him. **If
building this reveals a second wrong derivation, REPORT it, do not fix it.** No retrain, no promotion.

**Verify:** the surface reads the slot structure from the league snapshot, not from the constant's comment; it
states rank, reason and ppg per position; it flags the receiver mismatch on today's data without anyone hard-coding
that mismatch; and it says plainly which part of the derivation is an assumption.
