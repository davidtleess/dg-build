# DG-041 — The inputs gate is permanently red, because participation can never serve the season it is asked for

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** found 2026-08-25 while fixing DG-023's two false words. David ruled the words-only scope
and asked for the rest to be filed rather than folded in.

**Problem:** `feature_refresh` will report `inputs_degraded` on **every run, forever**, and after
DG-023 the reason is finally stated truthfully — which is what makes the permanence visible.

`_load_stream_isolated` (`scripts/run_feature_refresh.py:92`) tries the full season window, then the
window minus its last season. Participation's upstream ceiling is `get_current_season(roster=True) - 1`
— it is *by construction* one season behind — so the first attempt raises `ValueError` **always**, a
fallback is always recorded, and `summarize_input_provenance` always degrades the artifact.

Today four of five streams step back, which reads like an offseason condition that Week 1 will clear.
It will not. `pbp`, `player_stats` and `snap_counts` go live once 2026 parquet exists upstream;
participation never does. A gate that is always red carries exactly as much information as one that
is always green — the failure mode `test_healthy_inputs_still_grade_fresh` was written to forbid, and
the same cry-wolf disease DG-023 was opened to cure, one level up.

**How we know:**
```
$ .venv/bin/python3.14 -c "import nflreadpy; nflreadpy.load_participation(seasons=[2026])"
ValueError: Season must be between 2016 and 2025          # structural, not an outage

$ sed -n '92p' scripts/run_feature_refresh.py
    for window in (seasons, seasons[:-1]):                 # ceiling is never lowered per-stream

# the reader, with 2026 upstream data present and participation unchanged:
degraded=True
EARLIER SEASON: participation (45,184 rows; season not reported by source; ValueError)
  | LIVE: pbp 2026, player_stats 2026, rosters 2026, snap_counts 2026
```
That last line is the whole ticket: one stream, doing exactly what it always does, holding the gate.

**Done looks like:** a stream is not reported degraded for a limit it can never exceed. The cheapest
shape is a per-stream CEILING beside the existing floor in `_STREAM_LOADERS` (participation already
carries a floor of 2019), so the window never requests a season the source cannot serve — no refusal,
no fallback, no daily false alarm, and `fallback_used` goes back to meaning *something happened*.
A test drives a stream whose ceiling is below the window and asserts no fallback is recorded.

Whoever takes it must check the blast radius on `source_hash`: the window feeds `_source_provenance`
(`:277`), so narrowing it changes the hash and will make the next run look like a content change.
That is a real consequence, not a blocker — but it should be a decision, not a surprise.

**Not urgent, and that is a measurement.** Nothing is wrong with the DATA — participation loads
45,184 good rows and the features it feeds are populated 498/505. What is wrong is that the operator
is told "degraded" every morning until the word stops meaning anything, which is precisely the state
DG-023 found the gate in.

**Depends on:** nothing. **Related:** DG-023 (fixed the words on this same surface; this is the
sentence underneath them), DG-039 (also a producer fix that could not be made in config), DG-034/036.

---

**Notes**
Deliberately NOT bundled into DG-023. That ticket's own instruction is "fix the words, keep the gate",
and DG-023 shipped 10 days before the 09-04 freeze; this one touches the producer's season window and
the source hash, which is a different risk class and deserves its own decision.
