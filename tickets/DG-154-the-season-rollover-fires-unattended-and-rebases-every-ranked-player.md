# DG-154 — The season rollover fires unattended around 2026-09-15 and silently rebases every ranked player from a 17-game measurement to a 4-game one

**Layer:** 2 (+3) · **State:** todo · **Lane:** — · **DG 3.0** · **model robustness / season safety · URGENT, fires by itself**
**Source:** found independently by TWO of three planning lenses in the ranking-quality workflow, 2026-09-04, each ranking
it #1 of everything on the board; the served path re-verified by Greg (`davidleess-eb [a78c76]`) before filing.
Filed 2026-09-04 14:0x ET.

**Problem:** the daily chain runs `run_feature_refresh.py` with **no `--season-end`**
(`scripts/run_daily_chain.py:59-60` — argv is the interpreter and the script path, nothing else), so
`run_feature_refresh.py:381-386` derives `season_end = int(player_stats["season"].max())` from the live feed and
`:400` sets `inference_season = season_end`. **The moment nflverse publishes the first 2026 `player_stats` row —
roughly 2026-09-15 — `inference_season` flips 2025 → 2026 with nobody in the loop.** Then
`feature_assembly.py:107,127` keeps only `(feature_season < inference_season - 1) | (feature_season ==
inference_season)`, dropping the COMPLETED 2025 rows, and `assemble_engine_b_dataset.py:59,178` filters every season
to `games_t >= 4`.

**Two consequences, in order.** (a) For roughly three weeks the 2026 partition is empty, candidate validation fails
"no rows for inference season 2026", and the publish is BLOCKED fail-closed — correct behaviour, but it exits 1 every
morning for a right reason, which masks a genuine feed failure. (b) Then, as players cross four games, the board
returns **rebased**: `ppg_t` silently stops meaning "a 17-game season" and starts meaning "the four games he has
played so far". `ppg_t` carries the largest coefficient at every position (QB 0.262 in the published fold vs
`ppg_t_minus_1` 0.120), so this is not a small drift.

**Sized by the workflow (treat as measured-once, re-derive before acting):** information loss on `ppg_t` roughly one
third pooled and roughly three quarters at QB; ranked players roughly 505 → 291 during the transition. Both figures
want re-measuring in the fix.

**⛔ THIS IS DAVID'S DECISION, NOT A MODELLING DETAIL.** The question is what the board is FOR during a season:
- **(A) Stay on 2025 for the season.** The ranking keeps meaning "based on his last complete season". Correct for a
  two-year dynasty horizon and stable all year — but the board does not react to 2026 football at all, and **David
  must be told, or he will read a frozen board as a broken one.**
- **(B) Advance to 2026 at a threshold he sets** (e.g. once a player has N games, or after week N), so the board
  starts reflecting this season on a date he chose rather than the day a feed happened to publish.
- **(C) Carry both** — rank on the complete season, show current-season form as its own signal.
Pinning wrongly is worse than the default, because then we own it. Do not choose for him.

**Done looks like:** the rollover cannot happen unattended — the season basis is explicit in the served path, not
derived from whatever the feed last published; a test proves that publishing a 2026 row does NOT silently change the
basis; the fail-closed window is distinguishable from a real feed failure in the alert (per DG-136, a refusal must not
read the same as a break); and whatever David rules is stated on his screen when it takes effect.

**Anti-scope:** no model retrain, no promotion (David's word, via DG-058/059 which are unbuilt); no change to
`MIN_GAMES_THRESHOLD`; nothing under `.oa3`.

**Depends on:** nothing. **Blocks:** everything that grades the board this season, including DG-018 and DG-152 —
they would grade a board this event corrupts.

---

**Notes**
