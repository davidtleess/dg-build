# DG-038 — `dg-land.sh` cannot merge into any base that is checked out somewhere

**Layer:** process  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** hit while landing DG-036, 2026-08-24. The gate passed and the merge step died.

**Problem:** `dg-land.sh:106-113` creates a detached temp worktree at `origin/$BASE` and then runs
`git checkout -q -B "$BASE" "origin/$BASE"` inside it. Git refuses to check out one branch in two
worktrees, so the merge dies whenever `$BASE` is already checked out anywhere — which is the normal
state of the trunk. `~/dynasty-genius-product` holds `feature/outcome-loop-week1`, so **every ticket
based on the working trunk fails at the merge**, after the full suite has already run.

It has not bitten `main` only because nothing currently has `main` checked out. The moment anything
does, `--from main` breaks the same way. This is not a branch-specific accident.

**How we know:**
```
$ ~/dg-build/bin/dg-land.sh DG-036 --from feature/outcome-loop-week1
...
6301 passed, 40 skipped, 361 warnings in 46.32s
→ merging into feature/outcome-loop-week1
fatal: 'feature/outcome-loop-week1' is already used by worktree at '/Users/davidleess/dynasty-genius-product'

$ git worktree list | grep outcome-loop-week1
/Users/davidleess/dynasty-genius-product   e6995967 [feature/outcome-loop-week1]
```

**Two consequences beyond the failed merge**, both observed:

1. **It leaves the temp worktree behind.** `~/dg-wt/.land-DG-036` survived at a detached HEAD, and
   `git worktree list` showed it. The `rm -rf "$TMP_WT"` at `:108` only runs on the NEXT attempt, and
   the `worktree remove` at `:115` is never reached. A second run of `dg-land.sh` would have removed
   it silently — so the failure self-conceals.
2. **The ticket is left claimed, the branch undeleted, and `State:` still `todo`**, because every
   bookkeeping step lives after the merge. A reader of the board sees an unstarted ticket whose lane
   is taken and whose tests already pass.

**Done looks like:** `dg-land.sh DG-NNN --from <a base that is checked out>` merges and pushes. The
merge does not need the branch checked out at all — a detached worktree can merge and push with
`git push origin HEAD:$BASE`, which is exactly how DG-036 was completed by hand. A test, or a dry-run
that exercises the merge path, catches this before the suite has burned 47 seconds.

Whatever the fix, it must also decide what to do about the **local** branch in the trunk, which after
a detached push is one commit behind `origin`. Updating it means touching a shared worktree that
routinely carries dozens of dirty files from other lanes, so `dg-land.sh` should probably say so
rather than do it.

**Depends on:** nothing. **Related:** DG-037 (the five other mechanisms that stopped tickets landing;
this is a sixth, in the half of the script DG-037's dry-run could not reach — `--dry-run` returns at
`:100`, before the merge block ever runs).

---

**Notes**
The `--dry-run` gate is what made this survivable: it reported "rebase clean, tests pass" and was
telling the truth about everything it tested. It simply never tests the merge. That is the cheap fix
worth considering first — a dry run that also proves the merge can start.
