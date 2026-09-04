# DG-146 — A small rookie marker on the roster row

**Layer:** 6 · **State:** todo · **Lane:** — · **DG 3.0** · **product truth / presentation · small**
**Source:** David, 2026-09-03 ~21:35 ET, answering Greg's plain-words question about DG-144's consequence #1 ("in the
roster table, the wide range was the only thing that made a rookie-model number look different from a measured one.
Now they look identical there. The player card still says which model scored him. Fine as is, or do you want a small
marker back in the table?"). Verbatim: **"2) small marker indicating theyre a rookie."** Filed 09-03 21:4x ET by Greg
(`davidleess-eb [a78c76]`), who builds it.

**Problem:** since DG-144 a rookie's number (rookie model: draft capital + age) and a veteran's measured number look
identical in the roster table. The card still says "Scored by: rookie model …"; the table says nothing.

**How we know:** DG-144 review panel, honesty lens, 2026-09-03 20:xx ET — `RosterAuditRow.tsx` renders no basis cue
once the range came off, and the number is not greyed by basis (David, 2026-09-01). Consequence #1 in DG-144's Notes.

**Done looks like:** each roster row for a rookie carries one small word, "Rookie", spoken through the copy dictionary,
next to the name's position/team line; the number itself is untouched (09-01 ruling stands: same weight, same colour).
A fixture rookie row shows the word and a veteran row does not — watched red first; `npm run gate` green; no new
horizontal overflow at 390 or 1440. **Keyed on the player fact (`is_prospect`), not the model basis — David said
"rookie".** A short-sample VETERAN whose number is a blend (DG-143's fill creates these) is not a rookie and gets no
marker; that case stays distinguishable on the card ("Scored by") and in the row's Details caveat only — recorded here
as the honest limit, not hidden.

**Anti-scope:** no styling of the number by basis; no change to the player card; no backend edit; nothing under `.oa3`.

**Depends on:** DG-144 (landed `505027b5`).

---

**Notes**
