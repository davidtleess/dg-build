# DG-149 — Two team signals on every player, in different spots: NFL team (FA when none) and league team (FA when unowned)

**Layer:** 6 (+2 for the league team name) · **State:** todo · **Lane:** — · **DG 3.0** · **product truth / presentation · small-medium**
**Source:** David, 2026-09-04 07:3x ET, verbatim: *"there should be a signal on every player NFL Team (including the FA tag
if they don't have a team) and Team = team they are on in my league i.e. woodbury riders or if they are a FA they get the
FA tag there too. they should be in different spots - no you can leave it by dleess"* — his answer to Greg's two
questions after DG-145 landed (header "no NFL team" instead of "FA"? → no: keep FA, and show BOTH signals in different
spots; "On your roster" instead of "Rostered by Dleess"? → *"no you can leave it by dleess"*). Filed 09-04 07:3x ET by
Greg (`davidleess-eb [a78c76]`); assigned to Bob (it extends DG-145).

**Problem:** the card carries the NFL team in its header ("TE · FA · age 27" — "FA" only when Sleeper marks him Active,
DG-137's rule; blank for Inactive/IR with no team) and, since DG-145, a league-ownership line naming the OWNER
("Rostered by Dleess" / "FA · nobody in your league owns him"). David wants two clearly separate signals on every
player: **NFL team** — the team, or "FA" whenever he has none (this settles DG-145's unbuilt NFL-team discrepancy:
"FA" for ANY missing NFL team, on every surface, not only when Active); and **league team** — the fantasy TEAM NAME he
is on in David's league (e.g. "Woodbury Riders"), or "FA" when nobody owns him. The same word "FA" in both spots is
fine because the spots are labelled and apart. The owner line stays as it is.

**How we know:** David's words above; DG-145's measurement (Bob, 09-03 23:38 and 09-04 07:29 ET): roster row prints
"FA" for any missing NFL team, the card only when Active, movers print nothing; 5,950 cards carry both meanings today;
Hill / Ekeler / Joly print "FA" directly above "Rostered by …".

**Done looks like:** (1) NFL team: one rule in one place — "FA" for any player with no NFL team — on the card header,
the roster row and the movers (DG-137's SOURCE is unchanged; only the null → "FA" mapping is unified). (2) League team:
the card shows the league TEAM NAME from the latest league snapshot (Sleeper users' `team_name` for the owning roster —
measure first that the snapshot carries it; where a manager has no team name, fall back to the owner display name and
say so), or "FA" when unowned, dated the way DG-145 dates ownership; placed apart from the NFL team under its own
label. (3) Tests red first: Inactive player with no NFL team → "FA"; owned player → his league team name; unowned →
"FA"; David's own player → his team name AND "Rostered by Dleess" retained. Gate green; openapi regen if the API gains
the team name (never hand-edit `frontend/openapi.json`).

**Anti-scope:** no change to the owner line's wording; no change to where the NFL team comes from; nothing under `.oa3`.

**Depends on:** DG-145 (landed `024de1ac`).

---

**Notes**
