# DG-024 — DAVID'S CALL: does Engine B's PPG mean regular season, or all games?

**Layer:** 3  ·  **State:** waiting on David  ·  **Lane:** —  ·  **DG 3.0**

**The question:** `fetch_and_agg_stats` has no `season_type` filter, so postseason games are counted
in points-per-game. That is a definition, not a bug, and only David can set it.

**What it costs:** 162 players have postseason rows; **160 of them have a different PPG because of
it**, mean absolute change 0.412. **Six players cross the eight-game modelling gate only because
postseason games are counted** — they are in the model solely on that basis.

**If the answer is "regular season only":** Engine B, the P90 ceilings, replacement level, xVAR and
calibration all need rerunning, because every one of them is derived from PPG.

**If the answer is "all games":** nothing changes, and the definition gets written down so this
question doesn't come back.

**Done looks like:** David says which one, and it is recorded.

---

**Notes**
This was routed to David rather than quietly patched in code. That was the right call — a silent edit
here would have changed every valuation on the board without anyone deciding to.
