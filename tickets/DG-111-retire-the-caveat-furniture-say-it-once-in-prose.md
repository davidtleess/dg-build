# DG-111 — Retire the caveat furniture: say it once, in prose, where it matters

**Layer:** 6  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**  ·  **frontend-only · DG-091 phase 2A**
**Source:** David, 2026-08-29, verbatim: *"I really don't care for the caveats and the hard wording
governance."* Direct consequence; builds without the phase-2B design answers.

**Problem — counted on his live screen:** `DisclosureLine` ("Descriptive only — not
decision-grade.") renders SIX times on the front page and again on every other surface; "Status:"
renders six times; caveat blocks stack at region, subsection and row level simultaneously; the
right rail carries FEED DIAGNOSTICS + RECEIPTS + "Movement history / Series pending" panels; the
player card repeats "Experimental", "Decision support only", "No counter-argument available".

**Build:** delete the stamped furniture from the surfaces. Where a fact genuinely changes what
David would do, it survives as ONE natural sentence in the flow, placed where it applies
(e.g. stale: *"Two of our nine overnight feeds ran a day behind, so a few prices are from
Thursday."*). Diagnostics/receipt CONTENT moves behind the existing receipt triggers
(`ui/ReceiptTrigger.tsx`) and a health sheet — invisible until asked for, complete when asked.

**⚠ THE LOCKED COPY — David's ruling is the sign-off:** `DISCLOSURE_LINE` is marked exact-string
LOCKED (`lib/copy.ts:73`) and TWO byte-locked mitigation paragraphs exist
(`trade/TradeLab.tsx:119-133`, `league-pulse/LeaguePulse.tsx:73-98`). They protect the register he
repealed. Replace them with prose AND record the replacement copy verbatim in this ticket so his
review of the finished screen is the review those locks exist to force.

**Honesty law (binding):** furniture goes, FACTS STAY. Stale must still say it is stale; an
unscored player must still say it is unscored; nothing may imply a number is fresher, more
validated, or more decision-grade than it is. Deleting a truth-bearing behavior instead of
rewording it is a BLOCKING defect.

**Done looks like:** at most one honesty sentence per region on a healthy morning and zero
stamped disclosure lines; every retired string either reworded in place or reachable in a receipt;
a test proving the stale-morning and unscored-player paths still SAY so; verified in-browser.
