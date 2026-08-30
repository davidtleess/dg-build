# DG-111 — Retire the caveat furniture: say it once, in prose, where it matters

**Layer:** 6  ·  **State:** todo  ·  **Lane:** Davids-MacBook-Pro-80214  ·  **DG 3.0**  ·  **frontend-only · DG-091 phase 2A**
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

---

## BUILT 2026-08-30 — the replacement copy, recorded verbatim

David's 2026-08-29 ruling is the sign-off that released the exact-string lock on `DISCLOSURE_LINE`
and the two byte-locked mitigation paragraphs. Every replacement string is recorded here so his
review of the finished screen is the review those locks existed to force.

### A. The stamps that are simply gone (furniture, no per-surface fact)

| Retired string | Rendered where | Why it carried no fact of its own |
|---|---|---|
| `Descriptive only — not decision-grade.` | `DisclosureLine` primitive → Daily What-Changed ×7, ChartFrame (every chart), player card, Trade Lab, League Pulse header, Roster Capacity, System Health, Accuracy Tracker | Stated the backend register `decision_supported=false`. Not a fact about any number on the screen; the honest reading of it now lives in ONE sentence (§B1). |
| `Experimental — not decision-grade.` | Roster Audit header + Roster Audit filter bar | Same register, twice on one surface. |
| `Decision support only` | Player inspector preview | Same register on a panel that renders no grade, score or delta at all. |
| `decision_supported = false` | Model Trust truth panel | A raw backend field name rendered at the user. Replaced by §B1. |
| `Experimental — not validated` | Model Trust truth panel | Replaced by §B7. |
| `Status: ok` ×5 + `Context caveats: captured_at_vs_report_generated_at — fresh (age 0h)` ×5 | Daily What-Changed structural sections | Five identical paragraphs of nothing on a clean morning. Replaced by §B4 — silence when clean, a sentence when not. |
| `Status: Synced` / `Status: Degraded` | Daily tape | A third restatement of the two tape lines above it. Replaced by §B8. |
| `No counter-argument available` · `No top drivers available` · `No risk flags available` · `No caveats available` · the `Experimental` badge | Player card evidence section | Absence of content is not content. Now render nothing — with one guard, §B3, so an all-empty block can never read as a clean bill of health. |
| `Movement history — Series pending. History accrues one verified capture per day; the line begins once enough days are on the books.` | Daily What-Changed right rail | Replaced by §B9, said once beside the blank trend slots it explains. |
| `FEED DIAGNOSTICS` + `RECEIPTS` rail panels | Daily What-Changed right rail | Content intact, moved behind §B10. |

### B. The replacement copy, verbatim

**B1 — `MODEL_STANDING_SENTENCE` (`lib/copy.ts`), replaces `DISCLOSURE_LINE`.** Rendered ONCE on
the player card (foot) and ONCE on the Model Trust panel — the two places the model's standing
changes how you read a number:
> Our model is a sharp second opinion, not a proven market-beater — weigh it accordingly.

**B2 — the unscored player** (`player/PlayerDetailCard.tsx`), replaces the `Experimental` badge over
`No active model score`. The producer's own `degradation.message` still renders verbatim beneath it:
> Not scored yet — we don't have a model score for {name}. Anything the market says below is real; the projection stays blank until our next model run.

**B3 — the all-empty evidence guard** (`player/EvidenceSection.tsx`). Only when the counter-argument,
drivers, risk flags and caveats are ALL absent:
> We don't have evidence notes on this player yet — that means nothing is written down, not that there is nothing to say.

**B4 — the stale morning** (`what-changed/DailyWhatChanged.tsx`), replaces
`Stale data caveat — the capture is 27.5 hours old. The tape below reflects the last verified capture, not this morning.`
Three branches, one sentence each:
> This morning's capture didn't land — everything below is {n.n} hours old, the last verified snapshot, not today's.

> We couldn't read when this data was captured, so treat everything below as the last verified snapshot, not today's.

> These numbers are as of {capture time}.

("as of", never "current": the report can be up to 26h old and still sit under the stale threshold.)

**B5 — the front-page subtitle**, replaces
`A daily delta surface (what changed since the prior snapshot); no verdict, no nominated move.`:
> What changed on your roster and around the league since the last snapshot.

and the market region's note, replacing `Price-discovery deltas — market overlay only, isolated from model output.`:
> Market prices — what the dynasty market is paying, kept separate from our own projections.

**B6 — a degraded region**, replaces the stacked `CaveatBlock`s. The producer's raw token is
humanized on screen and kept VERBATIM in the element's `title` and in the receipt sheet:
> Heads up: the market side came back degraded — {reason} — so treat the prices below as provisional.

> Heads up: the model side came back degraded — {reasons} — so treat the model numbers below as provisional.

> Heads up: {basis} is {n} hours old and flagged stale, so this is the last verified read rather than a fresh one.

> Heads up: part of it did not come through — {reason}.

and the divergence caveat block becomes:
> These are counts of divergence cards, not a proven edge — we have not validated that they predict anything.

**B7 — `trade_lab_fe_mitigation_v1`** (`trade/TradeLab.tsx`). Replaces the byte-locked paragraph AND
the disclosure line under it. Every protected fact survives — no win/lose verdict is computed, fit
is not judged, the two pricings stay separate rather than blended, a stale or missing price says so
in its own lane, the call is the manager's:
> We price both sides two ways — what the dynasty market is paying, and what our model says — and keep the two apart instead of blending them into one number. Where a price is stale or missing, that lane says so. We don't call the winner and we don't judge whether the deal fits your team: that part is yours.

**B8 — `league_pulse_fe_mitigation_v1`** (`league-pulse/LeaguePulse.tsx`). Same DOM position, ahead
of every panel; the four-signal weight table below it is untouched. Every protected fact survives —
the labels are COMPUTED from four named roster signals, the weights are disclosed, and a manager's
real intent, private valuations and willingness to trade are explicitly unobservable:
> We label each team contending, rebuilding and so on by reading four things off its roster — starter-weighted model value, roster age profile, early draft-pick balance, and taxi/development stash — weighted as shown below. That is our read of the roster, not a read of the manager: what they actually intend to do, how they really value their own players, and whether they want to trade at all are things nobody can see from here.

**B9 — the Model Trust truth panel** (`trust/TrustTruthPanel.tsx`). Replaces
`Consensus-competitive, edge unproven. Engine B is statistically tied with DynastyProcess ECR expert consensus; per-fold NDCG-diff bootstrap CIs include zero.`
Not one of its three facts is softened; the bootstrap-CI evidence moves to the study on the same
surface (FoldTable / GateMatrix), where an NDCG diff belongs:
> Honest read: our model ranks players about as well as expert consensus, but it has not proven it beats the market — measured over our test seasons, the edge could genuinely be zero.

and, replacing `Experimental — not validated`:
> Nothing here has been validated against a live season yet — this is a lab result, not a track record.

**B10 — the League Pulse header**, replaces `EXPERIMENTAL — a read-only league snapshot.` + the
`Diagnostic Workspace: …` paragraph + the disclosure line (three stamps → one sentence):
> Your league at a glance — who's contending, who's rebuilding, and who to call. It's a read-only snapshot: we read each roster, we don't read minds.

**B11 — Roster Capacity**, replaces the stamp + `Capacity facts and value-at-risk ranges; no verdict, no nominated cut.` + `Artifact status: ok`:
> Where your roster is tight, and what each cut would cost you.

> Heads up: this capacity read came back {status}, so treat the ranges below as provisional. *(only when not ok)*

and, replacing `Candidates sorted by cut exposure rank as diagnostic order — not a cut sequence.`:
> Sorted most expendable first — if you have to cut someone, start at the top.

**B12 — Roster Audit header**, replaces `Status: active` + the stamp (silent when healthy):
> Heads up: this roster read came back degraded — treat the numbers below as provisional.

**B13 — the daily tape**, replaces `Status: Synced` / `Status: Degraded` (silent when healthy):
> Some of this data is behind — the lines above say which part.

**B14 — Accuracy Tracker**, replaces the stamp; the Model Input Fidelity paragraph is NOT a stamp
(it says what the scorecard measures) and is kept, reworded:
> Model Input Fidelity checks one thing: whether what players actually did on the field matches what the model assumed they would do. It grades our inputs, not the players.

**B15 — the receipt sheet** (`what-changed/DailyWhatChanged.tsx`), which now holds the whole
retired rail, shut by default, under the summary `Where this comes from`: report build time, market
capture window + source, model window, projection-basis line, feed statuses, and
`Producer reasons, verbatim: …` — every raw token the surface humanized upstairs.

**B16 — `describeStatusToken` fallback** (`lib/copy.ts`): an unmapped token is now HUMANIZED
(`market_snapshot_stale` → `Market snapshot stale`) instead of rendering raw. Reformat only — no
word added, removed or reordered — the `console.warn` still fires, and every call site keeps the
verbatim token in a `title` attribute or the receipt sheet.

### C. Proof

- Real chromium, both builds, both widths. Stamped furniture across the seven nav surfaces:
  **BEFORE (live trunk :8000) 14 at 1440px and 14 at 390px → AFTER (this branch) 0 and 0.**
  Before, per surface: Daily What-Changed 7 · Roster Audit 2 · Trade Lab 1 · Roster Capacity 1 ·
  League Pulse 1 · Model Trust 1 · Accuracy Tracker 1. After: 0 on every one.
- Stale morning driven in a real browser at 1440 and 390 (preview aged the report by 27.5h):
  renders `This morning's capture didn't land — everything below is 27.5 hours old, the last
  verified snapshot, not today's.`, keeps `.dg-wc--stale`, zero stamps, the word "caveat" gone.
- Unscored player + all-empty evidence proven by render tests
  (`player/PlayerDetailPage.test.jsx`, describe "DG-111 the unscored player still says so, in prose").
- Tree-wide source scan `ui/retiredFurniture.test.js` fails if any authored surface reintroduces a stamp.
- `npm run test` 351/351 · typecheck 0 · lint 0 · banned-language 0 · build 0 ·
  `pytest tests/contract/test_system_tier_readiness_t3.py t4.py` 21 passed.

### D. Residual, NOT in this ticket's scope

The player card still renders raw evidence keys from the backend
(`age_not_near_position_cliff`, `no_market_overlay`, `no_internal_value_signal`,
`engine_b_not_decision_grade`, `Signal completeness 83% — missing: ppg_t_minus_1, …`). Those are
DG-091 §3.5 copy-dictionary work, not stamped furniture; they are named here so the gap is not
mistaken for a miss.
