# DG-134 — The forward capture's provenance records `training_cutoff: null` every morning because it reads a `season` column the runtime table does not have

**Layer:** 2 · **State:** open · **Lane:** unclaimed · **DG 3.0** · **backend / capture provenance · cosmetic today, a lie tomorrow**
**Source:** DG-133 follow-up (filed 2026-09-01 in its LANDED section; ticketed 2026-09-02 06:05 by Tower).

**Problem:** `src/dynasty_genius/capture/model_forward_capture_driver.py:116-129`
`_derived_training_cutoff` walks the feature CSV bytes and, for every `training_eligible == true`
row, reads `record["season"]`. The live table (`app/data/features_runtime/engine_b_features_runtime.csv`,
40 columns) has no `season` column — the season is `feature_season` (column 19). The `KeyError`
is swallowed by `except (KeyError, ValueError): continue`, so `seasons` stays empty and the
function returns `None`. The capture's provenance-hash subset therefore records the training
cutoff as `null` and labels it "derived" — a value that reads as "unknown" when the truth is
"2023, every morning".

**Why it matters:** the provenance record is the capture's promise about what the model was
trained through. `null` is not a refusal, it is a silent blank in a hashed field — the next
person who reads it will not know whether the cutoff was unavailable or the code was wrong.
It does not change the capture's row count (12,226) or any served value — cosmetic today. It
becomes a lie the day someone compares provenance across vintages and reads `null == null`
as "same cutoff".

**Fix shape (small):** read the season through the assembler's own column name
(`feature_assembly.SEASON_COLUMN` / the same constant `inference_partition.py` uses), never a
bare literal; and make an all-`None` result on a non-empty eligible set FAIL CLOSED with a bare
token (`capture_training_cutoff_underivable`) rather than return `None` — the driver already has
the `abort(...)` path DG-133 wired. Contract test: a CSV with `feature_season` and eligible rows
derives the cutoff; a CSV missing the season column aborts with the token; a CSV with the old
`season` spelling also derives (or is refused — pick one and pin it).

**Anti-scope:** no assembler change, no CSV regeneration, no change to which rows are captured,
no `provenance_hash` algorithm change beyond the value now being real (note: the hash WILL
change once the cutoff is real — say so in the closeout so nobody reads it as drift).

**Verify:** run the driver read-only against the runtime CSV into a scratch DB; provenance shows
`training_cutoff: 2023`, `derived: true`; row count unchanged at 12,226.
