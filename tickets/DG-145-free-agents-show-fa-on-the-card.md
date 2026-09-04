# DG-145 — Free agents show "FA" on the card

**Layer:** 6 (+2 if the one-place decision lands in the served-team rule) · **State:** todo · **Lane:** — · **DG 3.0** · **product truth / presentation · small**
**Source:** David, 2026-09-03 05:59 ET (Tower session `6f07a6c0`, message 09:59:20Z), verbatim: *"I think free agents
should show 'FA' on the card."* Filed 2026-09-03 21:0x ET by Greg (`davidleess-eb [a78c76]`) under David's 19:5x ET
"then plan a few hours of work and get the team going"; assigned to Bob.

**Problem:** since DG-137 the served team is Sleeper's current team. A player Sleeper lists with no NFL team arrives
with a null team, and the product prints a dash where a manager expects the league-standard "FA"
(`frontend/src/roster/RosterAuditRow.tsx:73` prints `nfl_team ?? "—"`; the player card header, search results, trade
partners and movers are to be measured).

**How we know:** David's sentence above; `grep -n nfl_team frontend/src/roster/RosterAuditRow.tsx` → `:73` (2026-09-03
20:5x ET). First measurement, before any build: how many served rows carry a null team, and how many of David's 27.

**Reading of "free agent" — an ASSUMPTION stated to David 09-03 evening; correct it on his word:** NFL free agent, i.e.
no NFL team on Sleeper, the fact Sleeper itself prints as "FA". NOT "unrostered in his league", which is a different
fact (the league stores) and a different surface.

**Done looks like:** every surface that prints a team prints "FA" for a player with no NFL team, never "—" or blank for
that reason; a fixture with `nfl_team: null` renders "FA" in the roster-row and card tests, watched red first;
`npm run gate` green. Mint the word in ONE place (the dictionary's team formatter, or the served-team rule emitting
"FA") and record which — never per surface.

**Anti-scope:** no change to where the served team comes from (Sleeper — DG-137 settled it); no league-free-agent
logic; no backend edit unless the one-place decision lands there; nothing under `.oa3`.

**Depends on:** nothing.

---

**Notes**
