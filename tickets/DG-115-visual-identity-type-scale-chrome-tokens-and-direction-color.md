# DG-115 — Visual identity: the type scale, chrome tokens, and direction color

**Layer:** 6 · **State:** todo · **Lane:** — · **DG 3.0** · **frontend-only · DG-091 phase 2B WAVE 1 (lands first — waves 2/3 build on it)**
**Source:** `DG091-STUDIO-SPEC.md` §1/§5 + `DG091-DESIGN-BRIEF.md` §1 + David's 2026-08-30 panel.

**Build — the structure the token system is missing:**
1. **A display type scale above `--dg-text-lg`** plus weight and line-height tokens. Today 3 size
   tokens top out at 18px while ~18 literal sizes render; the morning-read hierarchy cannot be
   built on that. Body floor 15px; 13px becomes annotation only.
2. **Spacing steps 5-6** (1.5/2rem section rhythm is off-scale today) and **3 radius tokens**
   (control/card/round) replacing 33 raw literals.
3. **A neutral chrome/nav token family.** The shell currently borrows the DATA lanes — nav is
   `--dg-model-muted`, the inspector toggle `--dg-market`, the trust strip entirely model-muted.
   The two-lane law must mean only what it says: **blue = model, amber = market, nothing else.**
4. **⭐ DIRECTION COLOR — David ruled "Green up / red down".** Add movement tokens and use them for
   deltas. **This REOPENS an ENFORCED ban:** `frontend/src/styles/tokens.test.js:83-110` bans red
   (hue ≤30/≥350) and green (120-160) as "verdict hues". **Re-point that test at genuine buy/sell
   VERDICT styling — do not delete it.** The ban existed to stop the product implying a
   recommendation through color; David has green-lit recommendations in WORDS, and direction color
   is not a verdict. Record the amended rule in the test's own comment.
5. Declare elevation explicitly (the dark surface ladder + borders IS the depth system; no shadows)
   and either adopt or delete the dead tokens (`--dg-pos-*`, `--dg-dvs-floor` — zero consumers).

**Land inside the contracts:** every `var(--dg-X, fallback)` is pinned string-identical to `:root`
(`tokensI1.test.js:202-220`) — update fallbacks in the SAME change; regenerate
`rawCssAuditBaseline.json` and `visualCraftAuditBaseline.json` in the same change (they assert
exact equality and CANNOT be decremented by the generator — hand-edit with the count stated).
**Done:** tokens exist and are consumed; the verdict-hue test still fails on buy/sell styling and
passes on direction color; both baselines regenerated; nothing visually regresses at 1440/390.
