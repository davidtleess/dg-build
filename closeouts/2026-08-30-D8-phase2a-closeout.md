# DG-091 PHASE 2A — landed and live, 2026-08-30

David's order: *"create a sub agent workflow with tower, studio and whoever else is needed - and
start building."* Phase 2A built only what his OWN RULINGS settle — no new design opinions.

## Landed (all three, serialized, over the peer lane's DG-100/101/112)
- **DG-109 `59bab53e`** — the copy dictionary + an enforced render rule.
- **DG-111 `002a26bd`** — the caveat furniture retired across all seven nav surfaces.
- **DG-110 `921ec892`** — global player search, every dead end closed.

## Deployed and independently verified (not taking the agents' word)
Trunk `921ec892` · vitest **402 passed** (was 343) · dist rebuilt · live :8000 serving
`index-DtE3xmIx.js`. My own browser scan of the LIVE product, at 1440 AND 390, counting raw
tokens in visible text outside the declared exempt subtrees: **front page 0, player card 0**;
no horizontal overflow at either width.

## What the panels caught — the program's central risk, confirmed real
**52 findings fixed across the three lands.** The honesty lens earned the whole structure: the
first draft of the dictionary replaced opaque tokens with confident prose that was FALSE.
- `no_market_overlay` → *"Nobody is quoting a market price for him right now."* **BLOCKING** — the
  token is a scope statement (market overlay deliberately excluded), and the SAME card printed
  "Market value 5204 · 29th overall" three lines above it. Now ships as *"Market prices are
  deliberately left out of this read — it rests on our own numbers."*
- `no_internal_value_signal` → *"We have no value score of our own for him yet."* **BLOCKING** —
  contradicted by "Dynasty value 77.5" on the same card. The token actually means the
  age-weighted-risk calculation lacked an input. Now ships as *"We could not work out his
  age-weighted value risk — one of the two inputs it needs was missing from this record."*
- rollup `ok` → *"Nothing needs attention."* **BLOCKING** — the backend explicitly declines that
  claim (auxiliary degradation never degrades the root), so the card could have said "Nothing
  needs attention" directly above "9 reports: 8 fresh · 1 failed".
**The lesson: replacing a token with prose is an act of authorship, and confident prose can lie in
ways an opaque token cannot. Every mapping needs its producer read, not just a plausible reading.**

## ⚠ FOR DAVID'S EYES (judgement calls the lanes flagged rather than buried)
1. **DG-111 reworded `TRUST_TRUTH_COPY`**, a constant whose own header marks it "never
   free-typed", and moved away from wording recorded in its ticket as sign-off copy. Every factual
   claim is preserved; the shouted acronym NDCG is gone. Worth his read.
2. **`rawCssAuditBaseline.json` was edited BY HAND** in DG-111 (RosterAudit raw_font_size 3→2,
   total 45→44) because the generator can only insert, never decrement.
3. **The visual gate does not cover the surfaces where the worst regressions lived** — it visits
   only three surfaces, and DG-110's regression was outside them.
4. **a11y measurement trap, still live:** scanning a surface with no backend renders an error
   state with zero rows and reports a small clean violation count — a FALSE receipt. It produced
   one during this run and was caught. Also: axe reports COMPOSITED colors (it blends ancestor
   opacity) while getComputedStyle reports the CSS value — they disagree, and axe is right.

## Still on screen, for phase 2B (observed by me in the browser, not claimed by an agent)
The right rail still renders the daily tape as a MONOSPACE terminal pane; the degraded-model
sentence assembles awkwardly (*"the model side came back degraded — Two different model runs
landed on the same day… — so treat…"*, capital mid-sentence); "Model status" duplicates "Scored
by"; the model's second-opinion caveat prints TWICE on the card; the inspector still shows
"4 caveats · counter-argument unavailable" and "1 driver · 0 risk flags" (absence-as-content that
DG-111 removed elsewhere); "Value above replacement (xVAR)" still carries the acronym.

## David's design panel answers (2026-08-30) — phase 2B's brief
Trade voice = both pricings plainly, NO blended take/pass · parked surfaces leave the nav ·
green-up/red-down deltas (**reopens the enforced verdict-hue ban — re-point it at genuine buy/sell
styling, do not delete it**) · build the phone shell now.
