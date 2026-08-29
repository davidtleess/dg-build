# DG-089 — The front page's movers are not clickable: the product's most natural gesture does nothing

**Layer:** 6  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**  ·  **frontend-only**
**Source:** found by DAVID HIMSELF, first real user session, 2026-08-29 ~11:10 ("no cards are
displaying") minutes after SR-12 put the product in his browser. His panel ruling same hour:
**"Yes — build it today."**

**Problem:** the Daily What-Changed surface lists his roster's movers, but the rows are inert —
`<DailyWhatChanged />` is mounted with no selection callback (AppShell.tsx:171), while the
selection plumbing (selectPlayer → PlayerInspector → onOpenFullDetail → PlayerDetailPage)
already exists and is used by Trade Lab (AppShell.tsx:83-94, 208). Clicking a mover fires no
request and renders nothing (proven from the API access log: zero player fetches during David's
clicks).

**How we know:** AppShell.tsx:171 passes no props; grep shows onSelectPlayer wired only in
trade/ components; David's live session reproduced it.

**Done looks like:** clicking a mover row on Daily What-Changed opens the Player Inspector for
that player (same gesture-result as Trade Lab's search results), from which full detail is one
click — David clicks the biggest mover and sees the player, no search detour. Rows are real
buttons (keyboard + screen-reader reachable, DG-043 discipline). Frontend tests pin: row is a
button, click calls the selection callback with the right sleeper id, AppShell wires it.

**Constraints:** frontend-only (the DG-080/081 safety category — cannot touch capture). Reuse
the existing selection path; NO new endpoint, no schema change, no OpenAPI regen.

**Rollback:** revert one frontend commit.
