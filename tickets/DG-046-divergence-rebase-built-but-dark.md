# DG-046 — The common-cohort divergence fix is built, tested, and NOT WIRED IN

**Layer:** 5  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** 2026-08-26 six-layer completion audit (L5 auditor), facts re-verified same day.

**Problem:** the product's flagship market signal — model-vs-market divergence — is served daily
by the OLD mismatched-population computation. The repo's own docstring calls that delta
uninterpretable. The mandated common-cohort rebase EXISTS
(`src/dynasty_genius/market_divergence_rebase.py`) and is contract-tested
(`tests/contract/test_market_divergence_rebase_red.py`) but is imported ONLY by its test —
the daily job (`scripts/run_market_divergence_refresh.py`) still imports the old builder.
Every day it stays dark, the served number stays uninterpretable.

**How we know:** grep of importers; the launchd plist runs the old path; verified 2026-08-26.
**Mitigating fact (verified 2026-08-26):** `market_divergence_history.db` payloads store the RAW
components (`market_value`, `dynasty_value_score`) alongside the wrong percentiles — 549,460 rows
since 2026-07-09 — so the season archive is RECOMPUTABLE post-hoc. This is a should-fix-soon,
not a lost-forever emergency. Honesty markup (`decision_supported=false` gates) already prevents
decisions on the wrong number.

**Done looks like:** the daily job builds divergence through the common-cohort rebase; the
contract test that today only proves the module works also pins the production import path; one
production cycle observed serving the rebased artifact; the what-changed/PVO surfaces carry the
new number with unchanged honesty markup.

**Tier:** product code on a daily producer — pre-freeze candidate; after 09-04 it is not Tier 0
and waits for the season's end unless David rules otherwise. **Edge distance: DIRECT** — this is
the nearest lever that makes a served number more real.
