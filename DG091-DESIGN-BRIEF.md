# DG-091 Design Brief — the product David likes opening

**Drafted 2026-08-29 night (pulled forward from D10/soak on David's pick). Venue RULED same
night: the Studio fresh-eyes lane, handed THROUGH Tower (wall TW29-WALL-35 — crew lanes never
touch ~/frontend-studio). This brief is venue-neutral; David reviews it before any build.
BUILD is post-freeze, season weeks 1-2, ≤09-17. Acceptance: David opens the product on a
season morning and does not say "not great." Measured by his word, nothing else.**

Grounded in four read-only audits run tonight (visual identity, vocabulary, hierarchy +
navigation, ticket evidence) plus a coverage critique. Every load-bearing claim cites
file:line in `~/dynasty-genius-product/frontend` unless absolute.

---

## The verdict, and what it actually is

David, 2026-08-29 evening, first real day using the product: **"to be honest the front end
was not great"** — all four dimensions: how it looks · too dense/cluttered · confusing words
and numbers · hard to find things/clunky.

The diagnosis in one sentence: **the frontend is an instrument panel built by the same
machinery that builds capture pipelines — correct, honest, dense — and it has never had a
design day.** The evidence is unusually concrete because the honesty machinery preserved it:

- The strongest single exhibit for "confusing words and numbers": the full player card's model
  lane renders **eight numbers with no labels at all** (`player/ValuationTwoLane.tsx:52-61` — a
  manager sees `engine_b PROVISIONAL 7.4 2.1 62% 210 195 180`), and the preserved 08-25
  screenshot (`dg-build/preserved/2026-08-25-dg022-qa/dg022-tank-desktop.png`) shows raw
  pipeline keys `market_overlay_static_caveat` and
  `source_timestamp_is_fetch_time_not_publish_time` painted on screen verbatim as a run-on line.
- The strongest exhibit for "hard to find things": the surface listing ALL of David's players
  (Roster Audit) has **zero path to any player's card** (`shell/AppShell.tsx:175` passes no
  `onSelectPlayer`), and there is **no global player search** — the only search box lives in
  Trade Lab and clicking a result **adds the player to a persisted trade draft** as a side
  effect (`trade/TradeLab.tsx:73-81`, `trade/tradeState.ts:34-36`).
- The strongest exhibit for "too dense": the front page renders on the order of seventeen
  titled information blocks (derived count — masthead, disclaimer, 3 feed regions containing
  ~11 titled sub-blocks, 4 rail blocks; `what-changed/DailyWhatChanged.tsx:337-433`), with the
  same disclosure sentence appearing six times on one page.
- The strongest exhibit for "how it looks": three David-facing surfaces paint light-theme
  literals on the shipped dark canvas — Model Trust body text at **1.53:1–4.0:1** contrast
  (`trust/TrustConsole.css`, 26 raw oklch), Roster Capacity at **1.25:1** near-invisible
  (`roster-capacity/RosterCapacitySandbox.css:9`), and the player card's divergence label at
  a measured 3.23:1 (`dg022-axe-main.json`).

What this brief is NOT: a bug list. DG-089 (fixed), DG-090-A (pulled to tonight), and DG-043
own mechanics. This is the design direction those fixes land inside.

---

## Design direction — name it and keep it

The product's character is already real and worth keeping: **a calm, dark, honest terminal.**
Self-hosted Archivo + IBM Plex, OKLCH tokens, a constitutional two-lane color law (blue =
model signal, amber = market context, no verdict hues ever — enforced by test,
`styles/tokens.test.js:83-110`), a disciplined motion system with one sanctioned expressive
moment at daily open (`styles/motion.css:57-60`). The problem is not the character; it is
that the character is enforced at the token layer and then abandoned at the surface layer.
The direction: **finish the system it already wants to be** — no rebrand, no framework, no
light theme (theme toggle stays deferred I5 scope, `styles/tokensI1.test.js:222-229`).

---

## Section 1 — Visual identity, over the EXISTING token system

The token system (`src/styles/tokens.css`, 97 lines) is sound but thin: 3 type sizes topping
out at 18px, 4 spacing steps topping out at 1rem, **zero radius / shadow / weight /
line-height tokens** (30 raw radius literals in 10 values; zero box-shadows anywhere), and
~18 rendered font sizes where 3 are tokenized.

**1a. Retune (values only — structure exists):**
- `--dg-text-muted` (tokens.css:19) — the entire DG-090 46-node contrast census resolves to
  this ONE token on three backgrounds. Retune light L 0.55→~0.48; dark already passes by
  declared-value math (5.71–6.74:1). The census could not be statically reproduced (the axe
  evidence is un-archived, and the `dg-wc-settle` opacity entrance animation
  (`DailyWhatChanged.css:283-291`) is a plausible mid-animation measurement artifact) — so the
  gate re-run at `e2e/visual-smoke.spec.ts:397`, after animation settle, is the measurement of
  record either way.
- `--dg-caveat` (tokens.css:21) — 2.75:1 as text; either retune or forbid text usage (it is
  used as text at `model-scoreboard/ModelScoreboard.css:158`).

**1b. Add missing structure:**
- A display type scale above `--dg-text-lg` (the masthead/hero sizes 1.375–2.5rem are all
  literals today) plus weight and line-height tokens. The morning-read hierarchy in section 2
  cannot be built on a 3-step scale that tops out at 18px.
- Spacing steps 5–6 (1.5rem/2rem section rhythm is currently off-scale).
- Three radius tokens (control / card / round) replacing the 30 literals.
- **A neutral chrome/nav token family.** Today the shell borrows the data lanes: nav is
  painted `--dg-model-muted` (`shell/AppShell.css:63`), the inspector toggle `--dg-market`
  (:111), and the Trust Strip — the honesty surface itself — is entirely model-muted
  (`shell/TrustStrip.css`). The lane hues must regain their constitutional meaning: blue means
  "the model computed this," amber means "the market says this," and NOTHING else.
- Declare elevation explicitly: the dark surface ladder (bg 0.16 / surface 0.20 / raised 0.24)
  plus borders IS the depth system — flat, no shadows. Say so in the tokens file; delete or
  activate the dead tokens (`--dg-pos-*`, `--dg-dvs-floor` — zero consumers).

**1c. Convert the literal-painted surfaces to tokens** (until then, no retune reaches them):
`trust/TrustConsole.css` (26 raw oklch — the worst David-facing surface in the shipped theme),
`roster-capacity/RosterCapacitySandbox.css` (9 raw hex), `player/PlayerDetail.css:80-81`,
`trade/TradeLab.css:55`, and `project/ProjectTracker.css:12` (`#d18` — the only red-family
color in the codebase, evading the token-level verdict-hue ban).

**1d. Declare a responsive strategy.** Exactly FOUR non-motion media queries exist in the
entire app (`AppShell.css:118`, `DailyWhatChanged.css:81`, `ui/ui.css:404`,
`ModelScoreboard.css:212`). Every other surface — the player card (776px body at a 390px
viewport, DG-043), the 10-column Roster Audit table, League Pulse, Trust Console — has no
mobile adaptation at all. The identity must state what a phone gets: recommend a one-column
morning read with the player card collapsing to a single lane, and the dense diagnostic
surfaces honestly labeled desktop-first rather than half-broken.

**1e. Land inside the enforcement contracts** (the build must budget for these):
every `var()` fallback is pinned string-identical to `:root` (`tokensI1.test.js:202-220` — a
retune updates all fallbacks in the same change); the raw-CSS census baselines assert exact
equality (`rawCssAudit.test.js:112`, `visualCraftAudit.test.js:99` — regenerate in the same
change); ui.css must stay raw-color-free (`ui/uiCssContract.test.js`); banned hue arcs apply
to every token (`tokens.test.js:83-110`).

---

## Section 2 — Information hierarchy: the morning read

The test: one glance answers **"am I ok / what moved / what should I look at."** Today's front
page (`what-changed/DailyWhatChanged.tsx:337-433`) answers, in order: half, yes-but-buried,
and not at all.

**"Am I ok?" — promote the answer that already exists.** The roster-level numbers (Starting
lineup value, Weekly lineup strength…) render at the very BOTTOM of the feed inside Current
roster context → Team Value (:851-853, 951-986). They come from the same payload — promoting
them into a compact top-of-page standing block is a **layout change, not an API change**. The
data-trust half is already right: the "Synced · 9:02 ET" pill stays.

**"What moved?" — lead with the sentence, not the count.** The hero says "Your roster moved
N"; the single most valuable sentence (largest mover + delta) sits beneath it as small,
non-clickable basis text (:357-361). Invert: largest mover leads, clickable (the selection
sink is already passed in — `AppShell.tsx:177-179`), count becomes the qualifier.

**"What should I look at?" — descriptive salience, legally.** The no-verdict law bars a
nominated move; it does not bar ranking what's on screen by magnitude. The sanctioned pattern
already ships: OpportunityCards' sort caveat — "A larger value reflects a wider mathematical
magnitude, not a prioritized transaction order" (`league-pulse/OpportunityCards.tsx:42-43`).
Apply that pattern to the morning read: one "largest movements first" ordering across model
and market regions, carrying that caveat class. Salience without verdict.

**Density: one page, one disclosure per region.** Collapse the five baseline subsections
(Team Posture / Team Value / League Opportunity / Drop Pressure / Sleeper Snapshot — each with
its own Status line, DisclosureLine, and caveat block, :828-908) into one "Where you stand"
block with a single status line and ONE DisclosureLine. Today `DisclosureLine` renders six
times and "Status:" six times on one page; repetition is read as clutter, not honesty. The
honesty survives consolidation — every disclosure keeps existing exactly once per region.
Move "Feed diagnostics" and raw receipts behind their disclosure triggers (the receipt
primitive already supports this — `ui/ReceiptTrigger.tsx`).

Type floor: the dominant body size is 13px (`--dg-text-sm`) with pockets at 11.2–12.8px
(`TrustConsole.css`, `RealizedOutcomeScorecard.css:14`, `RosterCapacitySandbox.css:56`).
The morning read should sit at 15px+ body with 13px reserved for annotations — this falls out
of the section-1 type scale.

---

## Section 3 — Plain language: manager words, honesty intact

The inventory found ~25 pipeline-jargon families on screen, ~44 honesty-markup groups that
must SURVIVE reworded, and 17 bare-number sites. The rewording law is already written in the
codebase and should be quoted verbatim as the contract for this pass:
**"mathematically descriptive, never permissive — a caveat must never soften into
permission"** (`lib/copy.ts:4-6`).

**The mechanism: ONE shared display dictionary.** Four partial translation layers already
exist and are drifting apart — `describeStatusToken` (`lib/copy.ts:24-44`), the SystemHealthCard
display-name maps, `SIGNAL_DISPLAY` (`trade/MarketLanePanel.tsx:9-14`), and the
raw-name-one-layer-down title-attr convention (`DailyWhatChanged.tsx:958-984`). Consolidate
into one module; every surface imports it. Proof of the drift: `trade/DivergenceStrip.tsx:41-45`
renders the same signal tokens raw that MarketLanePanel maps — one import fixes it.

**Headline replacements** (full table in the vocabulary audit; all validated against
`shell/banned_vocabulary.json` — note standalone "depth" IS banned, so `depth_credit_xvar`
must not become "Depth credit"):

| On screen today | Manager language |
|---|---|
| xVAR (11 render sites, incl. raw `<dt>` keys) | "Value over replacement" — long-form on first use: "value above a waiver-wire replacement" |
| DVS / bare "62%" | "Model value" / "Better than 62% of TEs" |
| engine_b / Engine A/B / engine_path | "the player model" / "the rookie model"; ids demoted to receipts |
| divergence / card_type enums (`UNROSTERED_MODEL_MARKET_DIVERGENCE`) | "Where the model and the market disagree" + a card-type display map |
| `decision_supported = false` rendered literally (`trust/TrustTruthPanel.tsx:20`) | the standard DisclosureLine sentence — the FLAG stays visible; the snake_case goes (precedent already ratified at `lib/copy.ts:70-73`) |
| Δ / delta | "change" / "today's change" |
| vintage | "based on the {date} update"; hashes stay title-layer |
| capture ("last verified capture") | "last verified update" |
| artifact ("Artifact status: BLOCKED") | "Report status" + "This report was blocked from publishing, so no numbers are shown — better nothing than wrong" |
| degraded | "Running on older data — details inside" (visibility preserved) |
| z_score / "QB z_score 1.20 SURPLUS" | "QB strength: well above league average (+1.2 SD)" |
| value-at-risk / recovery range / parity | "Value you'd give up in the forced cuts" / "Value you could get back off waivers" / "Roughly even by the model's yardstick — not a verdict" |
| Kendall/Spearman/NDCG/"CI includes zero" | plain claim first ("could be zero — the measurement can't tell"), stat name secondary — the pattern `ModelScoreboard.tsx:171-176` already ships |
| "Inspector" / "Open full evidence card" | "Player card" / "Open full player card" |

**Bare numbers get labels and units.** Worst first: `ValuationTwoLane.tsx:52-61` (the eight
unlabeled spans), `DivergenceStrip.tsx:23-38` (two numbers on DIFFERENT scales — xVAR vs
FantasyCalc points — side by side with nothing saying so), projections "210 / 195 / 180" with
no unit or horizon labels (`RosterAuditRow.tsx:58-60`). Unit rule: every number carries its
scale on screen or in its receipt; note the live xVAR-percentile scale ambiguity (0–100 on
the player card vs a 0–1 fraction labeled "pct" in League Pulse `OpportunityCards.tsx:114-115`)
must be confirmed against the backend per field before relabeling.

**What survives untouched or sign-off-gated:** all ~44 honesty groups reword-only; and THREE
items are locked harder — `DISCLOSURE_LINE` is marked exact-string LOCKED (`lib/copy.ts:73`),
and the two byte-locked mitigation contracts (`trade/TradeLab.tsx:119-133`,
`league-pulse/LeaguePulse.tsx:73-98`) require **David's explicit sign-off before even
rewording**. Backend caveat sentences pass through verbatim in at least 8 components
(carrying `dynasty_value_score`, `Engine B`, `PRE_MODEL`), so this pass is NOT frontend-only:
scope a token→prose map for backend caveat strings or a backend copy change. The build phase
starts with a fresh live-surface label census (the static sweep and the preserved screenshots
show different vocabulary sets).

---

## Section 4 — Navigation: players reachable from anywhere

Today's click map: 2 clicks to the full card from the front page and Trade Lab (post-DG-089);
**impossible** from Roster Audit, Roster Capacity, and League Pulse (player names render
unclickable); the historical baseline before DG-089 was 4 interactions plus a typed search
through Trade Lab (the preserved dg022 spec path). The full card is a trap by design-gap: no
back button, browser Back does not close it, refresh loses it (code-proven: the id is plain
React state, `AppShell.tsx:77,168`; `?player=` hydration is an explicitly deferred, owned
decision — "I3-owned", `shell/useUrlSurfaceState.ts:2-4`).

**The four moves:**
1. **Global player search — a rewiring, not a feature.** `trade/AssetSearch.tsx` already hits
   the generic read-only catalog endpoint `/api/trade/assets?q=` with zod at the boundary;
   the trade-draft pollution lives entirely in Trade Lab's `onSelect` callback. Mount the same
   component in the shell with `onSelect = selectPlayer`: zero backend work, creates the
   missing inspect-without-adding path, retires the draft-pollution defect. Index players in
   the command palette too, and give the palette a visible trigger + placeholder (it is
   currently Cmd+K-only with an unlabeled input).
2. **Close every dead end via the existing sink.** The DG-089 comment says it itself: "one
   selection sink for every surface" (`AppShell.tsx:84-85`). Roster Audit is one prop
   (`AppShell.tsx:175`); then Roster Capacity, League Pulse pools, QB context cards, Trade Lab
   result lanes, and the front page's Entered/Exited chips and hero-basis name.
3. **Give the card an exit and an address.** A back affordance is the minimum; the honest fix
   is URL addressability (`?player=`), which is a **decision David must make** since it
   reverses a recorded deferral (I3) — this brief surfaces it rather than silently reversing it.
4. **Rank the rail by David's morning, not by build history.** Eleven flat items where parked
   surfaces sit at equal weight with live ones; group as: the morning read first, then his
   roster tools, then diagnostics, with parked items visually subordinate. Fix the empty
   inspector greeting first-load (opens containing only the word "Inspector" —
   `AppShell.tsx:73`).

---

## Constraints that stand (verbatim, from the ticket)

No-verdict law (no buy/sell smuggled in via design) · `decision_supported` stays visible ·
the tape's honesty function survives any restyle · zod boundaries untouched. Plus the
enforcement layer in section 1e, the banned-vocabulary file as the copy validator, and the
frontend-only safety category (nothing here can touch capture).

---

## Sequencing proposal

**Week-one build list (small, high-yield, each independently landable post-freeze):**
1. Roster Audit dead-end fix — one prop (`AppShell.tsx:175`).
2. Empty-inspector fix — one word (`AppShell.tsx:73`).
3. Global player search + palette player-indexing (the AssetSearch rewiring).
4. DivergenceStrip imports `SIGNAL_DISPLAY` — one import.
5. `--dg-text-muted` retune + gate re-run per the DG-090 protocol (fallbacks + baselines
   regenerated in the same change).
6. Hero basis largest-mover made clickable.
7. League Pulse raw `<dt>` keys → the existing display dictionary.
8. `#d18` literal swap in ProjectTracker.
9. Dead tokens: adopt or delete.
10. DG-043's three player-card furniture defects (labeled pairs fix also kills the run-on line).

**The design pass proper (Studio, via Tower):** sections 1–4 as a coherent restyle — token
structure additions, the morning-read layout, the display dictionary, nav regrouping — built
against this brief, reviewed by David before code.

**Decisions only David can make (surface with the brief, don't bury):**
- `?player=` URL addressability (reverses the recorded I3 deferral) — recommended yes.
- Rewording the two byte-locked mitigation contracts + the LOCKED DisclosureLine string.
- Whether the backend caveat-copy change rides this program or files separately.
- MarketLanePanel renders sent-side assets only (`trade/MarketLanePanel.tsx:76-88`) —
  intentional or defect; confirm before the design pass treats the lanes as symmetric.

---

*Evidence appendix: the four audit reports + coverage critique from 2026-08-29 night (session
scratchpad, summarized here with citations); preserved real-surface QA at
`dg-build/preserved/2026-08-25-dg022-qa/`; tickets DG-089 / DG-090 / DG-043 / DG-091.*
