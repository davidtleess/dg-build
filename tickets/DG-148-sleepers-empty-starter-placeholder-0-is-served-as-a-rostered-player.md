# DG-148 — Sleeper's empty-starter placeholder "0" is served as a rostered player

**Layer:** 3 (producer: league snapshot) · **State:** todo · **Lane:** Davids-MacBook-Pro-48631 · **DG 3.0** · **product truth / data · small**
**Source:** DG-145 adversarial review (data-correctness lens), 2026-09-04 00:1x ET, reproduced by two refuters and by Bob
on the served artifact; filed 09-04 07:5x ET by Bob (`davidleess-08 [b202b7]`) at Greg's request. Unassigned.
**⛔ Producer path:** `src/dynasty_genius/sleeper_universe.py` writes the league snapshot the 09:00 chain consumes. Post-chain
only, and not without David's word to touch a producer. This ticket is the record.

**Problem:** Sleeper fills an EMPTY starter slot with the string `"0"`. `_build_roster_context`
(`src/dynasty_genius/sleeper_universe.py:95-98`) unions `starters | taxi | reserve | players` and filters with `if pid`, which
`"0"` passes. So a phantom player `sleeper_player_id "0"` is written into the snapshot and the universe artifact as
`rostered: True, in_starters: True`, owned by whichever roster with an empty slot is processed LAST — today roster 9
(Dseidman). It is one of the "274 rostered" rows, it is `UNRESOLVED_IDENTITY` (no name, no position), and since DG-145
`GET /api/players/0` returns 200 with `league_ownership {status: rostered, owner_display_name: "Dseidman", roster_id: 9}`.

**How we know (2026-09-04 07:4x ET, read-only):** league snapshot `league-20260903T130044Z/snapshot.json`: roster 1
(David) has `"0"` in **7 of 9** starter slots, roster 9 in **1 of 9**; `"0"` is in NO roster's `players` list.
`universe_pvo_runtime.json` (18:00Z 09-03): exactly one row with `sleeper_player_id == "0"`: `player` all null,
`dg_status UNRESOLVED_IDENTITY`, `league_context {rostered: True, in_starters: True, roster_id: 9, owner_display_name:
"Dseidman"}`. `curl -s :8000/api/players/0` → 200, identity all null. It is the only `UNRESOLVED_IDENTITY` row among the
274 rostered (rostered by engine_path: ENGINE_B 219 / ENGINE_A 44 / PRE_MODEL 10 / UNRESOLVED_IDENTITY 1).

**Why it matters:** every "rostered" count in the product (274; `coverage.rostered_skill_players_missing_route`; the
trade catalog "rostered-players-plus-picks"; League Pulse roster sizes) is off by one, attributed to the wrong manager,
and the placeholder is reachable as a card. Harmless today; a real-name identity resolution on `"0"` would not be.

**Done looks like:** `_build_roster_context` defines rostered from `players | taxi | reserve` and treats `starters` only as
a flag on players already rostered (a starter that is not in `players` is a placeholder, not a player); `"0"` is dropped
explicitly with a comment naming Sleeper's convention; a unit test with a roster whose `starters` carry `"0"` asserts no
`"0"` key in the context and `in_starters` still True for the real starters; after the next 09:00 chain the runtime
artifact has no row `"0"` and rostered == 273 (or whatever the league truly holds); `GET /api/players/0` → 404.

**Anti-scope:** no change to the served-team, age or ownership rules; no frontend change.

**Depends on:** David's word to touch a producer (his standing guard on producers); land post-chain.

---

**Notes**
