# DG-047 — The morning report's staleness caveat cries wolf, training David to skip caveats

**Layer:** 6  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** 2026-08-26 six-layer completion audit (L6 auditor); threshold verified same day.

**Problem:** `src/dynasty_genius/what_changed/report.py:54` sets `_STALE_THRESHOLD_HOURS = 24.0`
on artifacts that refresh on a 24-hour cadence — a razor-thin margin, so ordinary same-slot
timing jitter flags healthy artifacts as stale. The audit measured the caveat firing ~6 mornings
out of 7. The caveat channel is the SAME channel a real SR-11-class failure would use to reach
David inside the product; months of false staleness teach him to skip exactly that channel.
The whole honesty-markup system only works if a caveat is rare enough to mean something.

**How we know:** report.py:54 read 2026-08-26 (threshold + its comment); firing frequency is the
L6 auditor's measurement — re-measure across a week of reports before choosing the new margin.

**Done looks like:** the staleness caveat fires only on genuinely-late artifacts (threshold =
cadence + a grace that absorbs same-slot jitter, e.g. the 26h bound SR-11 uses for marker
freshness — align, don't invent a third convention); a week of healthy mornings produces ZERO
staleness caveats; a deliberately held artifact still produces one.

**Tier:** small product-code change, Tier-3-shaped polish — pre-freeze only if slack allows,
otherwise week 1 of season. **Edge distance: ENABLER** — protects the trust channel every future
edge signal must travel through.
