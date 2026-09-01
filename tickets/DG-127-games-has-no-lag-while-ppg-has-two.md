# DG-127 — `games` has no lag while `ppg` has two, so the gate judges durability on one season

**Layer:** 3 · **State:** todo · **Lane:** — · **DG 3.0** · **backend / model · ENABLER FOR DG-128**
**Source:** 2026-09-01, Bob lane, verified on the served pickle `runs/20260831T204458Z/wr_v2.pkl`.

**Problem:** the 39-column Engine B feature table lags `ppg` TWICE (`ppg_t_minus_1`,
`ppg_t_minus_2`), lags `snap_share` ONCE (`snap_share_t_minus_1`), and lags `games` **ZERO**
times. There is no `games_t_minus_1` or `games_t_minus_2` anywhere in it.

`ENGINE_B_MIN_GAMES_T = 8` gates on `games_t` — **one season**. So the pipeline demonstrably
knows how to build lagged features, it built three of them, and the single durability signal
the gate actually depends on is the one left un-lagged. **The gate asks "is this player
durable?" and is handed exactly one season to answer with.**

Garrett Wilson (`00-0037740`): 2022 `games_t`=17, 2023 `games_t`=17, 2025 `games_t`=7. A
four-year professional with two full seasons on file is refused a score, and the dead-window
path then reaches OUTWARD for an Engine A college/draft prior while `ppg_t_minus_1`=14.82 sits
in the row being refused.

**Do:** add `games_t_minus_1` / `games_t_minus_2` to the feature assembly the same way the ppg
lags are built, and expose them so DG-128 can taper on evidence rather than cliff on one season.

**⛔ ANTI-SCOPE — read before planning.** Do NOT backfill 2024 feature ROWS as the fix. The gate
reads `games_t` off the 2025 row; a 2024 row cannot change it, and 2024 is ALREADY live as
`ppg_t_minus_1` for every player (provable: Wilson's 2025 `ppg_t_minus_2` = 12.541 equals his
2023 `ppg_t` exactly, so t−1 is 2024). The missing 2024 row is the DG-029 partition — deliberate,
pinned by `tests/contract/test_inference_partition_seasons.py`, and self-healing when the window
reaches 2026. Backfilling it is a plausible, expensive week that moves coverage by zero.

**Done:** `games_t_minus_1`/`_minus_2` are populated in the feature table with the same
availability-flag convention the ppg lags use, and a test pins that a player with a full prior
season is distinguishable from a true rookie at the same `games_t`.
