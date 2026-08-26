# DG-080 — Trade Lab asset search renders results for the WRONG query (SR-15)

**Layer:** 6  ·  **State:** doing  ·  **Lane:** ClaudeFable5-DG080-20260826  ·  **DG 3.0**
**Source:** season spec SR-15 (docs/strategies/2026-08-20-dg-SEASON-BUILD-SPEC.md:1301, draft
PERF-4, absorbs MR-8). Sprint-committed Tier 0, calendar slot D9 (Wed 09-02) — **pulled forward
to the evening of D4 (08-26) on David's "go ahead with SR-15"**, chosen because it is the one
committed sprint ticket that cannot contaminate Thursday's single-variable acceptance checks
(pure frontend; no producer, plist, or alert surface) and cannot collide with the unlanded
ticket/DG-045 branch (disjoint files).

**Problem (a correctness bug, not a latency one):** `frontend/src/trade/AssetSearch.tsx:37` fires
one fetch per keystroke — no debounce, no AbortController, no sequence guard — and line 26
renders whichever response resolves LAST. Each request parses ~30 MB server-side, so completion
order is scrambled. Spec reproduced 3/3: typing "brown" leaves the dropdown showing the results
for "bro" — **Brock Purdy offered as a match for "brown"**. One mis-click puts the wrong asset
into a trade evaluation.

**How we know:** the spec's 3/3 reproduction; code re-read 2026-08-26 18:5x — `onChange={(event)
=> void run(event.target.value)}` at :37, last-write-wins `setResults` at :26, both exactly as
the spec describes. The repo's own fix pattern exists at
`frontend/src/lib/useEndpointResource.ts:24-56` (AbortController + aborted-guard in catch), whose
header explicitly exempts "Trade Lab POSTs and typeahead search" — which is how this was left out.

**Fix (spec steps 1–5, verbatim scope):** query into React state; fetch driven by a `useEffect`
keyed on a 200 ms debounced query (timer cleared in cleanup); AbortController per request with
`controller.abort()` in cleanup and the `if (!controller.signal.aborted)` guard in the catch (an
abort must not render as an error and must not clear results); keep the min-length-3 guard and
safeParse-or-clear behaviour exactly as they are; **no caching, no retry** — one in-flight
request at a time is the whole fix.

**Done looks like:** two new tests in `frontend/src/trade/AssetSearch.test.jsx` — (a)
stale-result: stub `q=bro` slow / `q=brown` fast, type b-r-o-w-n with fake timers, assert no
"Brock Purdy" in the list, **watched RED against today's code first**; (b) debounce: type
`brown` char-by-char within 200 ms, assert exactly one fetch with `q=brown` (today: 3). Existing
four AssetSearch tests stay green (real-timer `waitFor` tolerates the 200 ms debounce). Then
`cd frontend && npm run gate` green, and land on `main` through dg-land.

**Tier:** 0 (wrong data on a decision surface). **Edge distance: DIRECT-adjacent trust repair** —
the surface the trade numbers travel through stops lying about which player it is showing.
