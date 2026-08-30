# DG-118 — Extend the visual evidence gate to every surface it is supposed to protect

**Layer:** 6 · **State:** todo · **Lane:** — · **DG 3.0** · **frontend-only · DG-091 phase 2B WAVE 3 (last — it must gate the finished work)**
**Source:** the 2026-08-30 closeout audit.

**Problem:** `frontend/e2e/visual-smoke.spec.ts` visits only `?surface=what-changed`,
`?surface=asset-primitive-capture` and `?surface=accuracy-tracker` (lines 459, 499, 515, 552, 581,
603). **Every defect DG-116 and DG-117 fix lives on a surface the gate never visits** — Trade Lab's
unbuilt styling, Roster Audit's 185px sideways scroll, Model Trust's 665px. A gate that cannot see
the breakage it exists to prevent is decoration.

**Build:**
1. Visit EVERY nav surface at 1440 and 390: no horizontal page overflow, axe clean, screenshots
   archived. Wide tables scrolling inside their own container is correct and must not be flagged.
2. **State the gate's real coverage honestly.** It runs under
   `emulateMedia({reducedMotion:'reduce'})` (:113), so the default-motion path readers actually see
   has NO browser-level a11y coverage. Either cover both paths or say plainly in the spec's own
   comment which path is gated. **Never buy green by excluding a rule.**
3. **Use axe's COMPOSITED colors, not `getComputedStyle`.** They disagree — axe blends ancestor
   opacity and is right. A `getComputedStyle` scan reports 0 failures on surfaces where the
   composited scan finds 21 (`.dg-shell__parked-badge`, 2.89:1 at cumulative opacity 0.65).
4. **Guard against the FALSE RECEIPT:** scanning a surface whose backend is unavailable renders an
   error state with zero rows and reports a small clean violation count. The gate must FAIL on a
   surface that rendered no content rather than passing it as clean.

**Done:** every nav surface gated at both widths; the reduced-motion limitation stated in the spec;
composited-color checking; a content-presence assertion so an empty error state can never pass.
Coordinate with DG-102 (the land gate is pytest-only and blind to frontend breakage).
