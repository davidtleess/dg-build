# DG-155 — Capture the 2026-09-08 consensus snapshot, or the only evidence that can settle the market question stops growing

**Layer:** 1 · **State:** todo · **Lane:** — · **DG 3.0** · **evidence preservation · HARD DEADLINE 2026-09-08 · half a day**
**Source:** the ranking-quality workflow, 2026-09-04 (21 agents, 13 of 14 candidate limiters killed); the archive state
below re-verified by Greg (`davidleess-eb [a78c76]`) on disk before filing. Filed 2026-09-04 14:2x ET.

**Problem:** the finding that a free consensus ranking orders players BETTER than Engine B rests on exactly FOUR
snapshots — `app/data/backtest/qb_validation/raw/dp_values/values_{2021,2022,2023,2024}-09-08.csv`, one per September 8,
all downloaded 2026-08-14. **There is no 2025 or 2026 snapshot and nothing captures one.** The daily market capture is
FantasyCalc, which is a different object with no historical validation. So the series that produced the only
market-versus-model evidence this project has is frozen at four points, and **2026-09-08 is four days away.**

**How we know:** `find app/data -name 'values_*.csv'` → exactly four files, 2021-2024, all `-09-08`, mtime 2026-08-14
(2026-09-04 14:2x ET). No producer references them: nothing under `scripts/` or `src/` writes that directory.

**Why the deadline is real and not a preference:** the series is annual and dated. A snapshot taken on 09-20 is not
comparable to four taken on 09-08 — roster news, injuries and camp movement between those dates are exactly the
information the comparison is trying to hold constant. Miss Monday and the honest next point is 2027-09-08.

**⛔ FIRST REQUIREMENT — LAWFULNESS, before any fetch.** Confirm the source's terms permit this capture and record the
finding in the ticket with the URL and the licence. This project's registry already prohibits KTC, FootballGuys and
Dynasty Nerds ([[project_review_verdicts_2026-09-03]]); do not assume DynastyProcess is different because we happen to
hold four of its files. **If it is not clearly permitted, STOP and report — do not fetch.** A new paid source is
David's word.

**Done looks like:** if lawful, `values_2026-09-08.csv` lands in the same directory, same shape, same date convention,
with its provenance recorded (URL, fetch time, sha256) and a note saying who checked the terms; the fetch is a
run-scoped, idempotent producer that can be re-run without clobbering; and 2025's absence is documented as a
permanent gap rather than quietly ignored. A test pins the file's shape against one of the four existing files.

**Anti-scope:** no scraping of any prohibited source; no change to the FantasyCalc daily capture; no model change; no
backfill of 2025 (it cannot be recovered — say so). Nothing under `.oa3`.

**Depends on:** nothing. **Blocks:** any future extension of the market-versus-model comparison.

---

**Notes**
