# DG-137 — The served team label is the model-side (2025 feature) team, not Sleeper's current team: 189 valued players disagree

**Layer:** 2 · **State:** open · **Lane:** — · **DG 3.0** · **product truth / identity · small**
**Source:** 09:00-chain rehearsal, verify-B agent (2026-09-02 06:3x, measured on the rehearsal artifact AND the live `universe_pvo_runtime.json`); sourcing lines verified by Tower; ticketed 2026-09-02 06:40 by Tower.

**Problem:** for modeled rows the universe artifact sets
`player.team = pvo.nfl_team or player.team` (`src/dynasty_genius/universe_pvo_batch.py:173`), and
`nfl_team` comes from the prediction/feature row (`scripts/build_universe_pvo_batch.py:336`,
`pvo_assembler.py:237`) — i.e. the team the player was on in the 2025 feature season. Sleeper's
current team is only the fallback. Both surfaces that show a team prefer that field:
`app/api/routes/players.py:315` (player detail: `{position} · {team} · age` in
`PlayerDetailCard.tsx:32`) and `app/services/roster_auditor.py:222`
(`nfl_team=player.get("team") or live_player.get("team")`). Measured: **189 ENGINE_B rows carry a
team that disagrees with the SAME snapshot's Sleeper team** (e.g. Emari Demercado served as ARI
while Sleeper has him KC→none). Between the 08-31 and 09-01 snapshots Sleeper cleared the team on
337 players and flipped 27 to Inactive — cutdown week is exactly when a 2025 team label is wrong
most often, and it is wrong on the card David opens to decide a trade. Pre-existing; not caused by
DG-133 or the rehearsal.

**Fix shape:** the served team is a *roster* fact, not a *model* fact — prefer Sleeper's current
team wherever a team is displayed, and keep the feature-season team only as the model's context
(rename it if it must be kept on the row: `feature_team` / `season_team`). One-line change at
`universe_pvo_batch.py:173` (flip the `or`) plus the same flip at `roster_auditor.py:222`, and a
contract test that a row whose Sleeper team differs from its feature team serves Sleeper's.
Check what `PRE_MODEL`/`INACTIVE` rows do today (they have no pvo, so they already show Sleeper's).

**Anti-scope:** no change to the value, the band, or any model input — `team` is not an Engine B
feature (`ENGINE_B_BASE_FEATURES`); no change to the roster route's own rows
(`roster_auditor.py:694` already reads the Sleeper snapshot); do not "fix" it by re-running
feature assembly — the feature table's team is correct FOR THE FEATURE SEASON and must stay.

**Verify:** count of ENGINE_B rows whose served `player.team` ≠ snapshot team goes 189 → 0 on the
next artifact; Demercado's card shows Sleeper's team; David's roster rows unchanged.
