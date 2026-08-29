# DG-090 — The daily tape overflows the desktop viewport by 5px, and the surface's a11y evidence gate has never passed

**Layer:** 6  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**  ·  **frontend-only**
**Source:** exposed 2026-08-29 when DG-089 repaired the visual-smoke harness (strict-mode
selector fixed; Playwright chromium installed — the harness had NEVER actually run).

**Problem A (the 5px):** `span.dg-ui-tape__fact` renders to right=1445 at a 1440 viewport —
`expectNoHorizontalOverflow` fails for the daily-open evidence bundle. PRE-EXISTING: probe-proven
byte-identical on MAIN's own bundle (live :8000, zero DG-089 buttons) and on the DG-089 branch.
Not a DG-089 regression; DG-089's rows measure 43px single-line (probe evidence in its ticket).

**Problem B (contrast census):** a manual axe run on the surface reports ONE violation class —
color-contrast [serious], 46 nodes (h4 group headers, headshot-fallback initials, meta labels,
disclosure lines, overlay notes, position spans) — all pre-existing element classes.

**Done looks like:** the daily-open evidence bundle (visual-smoke.spec.ts:397) passes end-to-end
on a clean tree: no horizontal overflow at 1440 and 390, axe returns [], screenshots + focus
capture archived. Fix the tape span's overflow (likely white-space/min-width on the fact span);
work the 46-node contrast list down to zero or to documented token changes.

**Constraints:** frontend-only; the tape is a ui/ primitive shared with other surfaces — check
its other consumers before changing it. Post-freeze unless David pulls it (purely cosmetic-eval
debt; nothing captures wrong data).
