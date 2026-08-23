# DG-037 — No ticket can land through `dg-land.sh`: the worktree fails its own gate four ways

**Layer:** process  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** measured end-to-end while building DG-034, 2026-08-23. Not inferred — every claim below is
a command that ran in `~/dg-wt/DG-034`.

**Problem:** `dg-work.sh` makes a worktree usable by symlinking the 15 GB shared store. `dg-land.sh`
refuses on a dirty tree (`:48`) and on any test failure (`:73`). Those two are in direct contradiction:
**the symlinks that make the worktree work are what fail the gate.** Four independent mechanisms,
none of them related to any ticket's own change.

**1 — three symlinks read untracked, so the dirty-tree check refuses.**
`dg-work.sh`'s in-flight landability fix writes its exclude file to
`"$(git -C "$DEST" rev-parse --git-dir)/info/exclude"`. In a worktree that is
`.git/worktrees/<TICKET>/info/exclude`, but **git only ever reads `$GIT_COMMON_DIR/info/exclude`**.
The exclude half has never taken effect; only the `skip-worktree` half works (that uses the
per-worktree index, which is why 72 phantom deletions do get cleared).
```
$ git rev-parse --git-dir            → /Users/davidleess/dynasty-genius/.git/worktrees/DG-034
$ git rev-parse --git-common-dir     → /Users/davidleess/dynasty-genius/.git
$ git status --porcelain
?? app/data/backtest
?? app/data/identity
?? app/data/sources
$ git -c core.excludesFile=<patterns> status --porcelain     # when git can actually read them
(clean)
```
The last line is the proof: the excludes work fine, they are simply written where git never looks.
**This is NOT the "git won't ignore paths with tracked entries" theory** — those 72 tracked files are
already `skip-worktree`'d and the exclude suppresses the symlinks regardless. One-word fix
(`--git-dir` → `--git-common-dir`), but the common dir is SHARED across every worktree, so the naive
fix appends duplicate lines on every run and leaks one worktree's paths into all others. Needs a
moment's design, not just the one word.

**2 — 13 tests fail because `git check-ignore` will not traverse a symlink.**
```
$ pytest "tests/contract/test_footballguys_phase_a_red.py::test_p0_runtime_paths_are_narrowly_gitignored[receipts.db]"
E  AssertionError: runtime path remains commit-eligible: app/data/footballguys/receipts.db
E  assert 128 == 0
   fatal: pathspec 'app/data/footballguys/receipts.db' is beyond a symbolic link
```

**3 — 3 tests fail because `frontend/node_modules` is not shared.**
`dg-work.sh` shares `.venv` but not `node_modules`, so any test shelling out to a node scanner dies:
```
$ pytest tests/contract/test_frontend_banned_language_linter_contract.py
E  code: 'ERR_MODULE_NOT_FOUND'   Node.js v24.15.0
```
Note the trailing-slash trap if this is fixed by symlinking: both `.gitignore:192` and
`frontend/.gitignore:1` say `node_modules/`, which matches a **directory** and therefore does not
match a symlink — a symlinked `node_modules` reads untracked and re-triggers mechanism 1.

**4 — 3 tests fail because a symlink resolves outside the worktree.**
```
E  ValueError: '/Users/davidleess/dynasty-genius-product/app/data/sources/nflverse_schedules'
             is not in the subpath of '/Users/davidleess/dg-wt/DG-034'
```
`Path.relative_to()` against a resolved shared path. Affects
`test_b21_schedules_capture_red.py`, `test_cfbd_fbs_schedules_capture_red.py`,
`test_identity_crosswalk_hardening_red.py`.

**How we know the 19 are not any ticket's fault:** stash the ticket's change, re-run, get the identical
19.
```
$ git stash push -- <the four changed files>
$ pytest tests/contract/test_{b21_schedules_capture_red,cfbd_fbs_schedules_capture_red,footballguys_phase_a_red,frontend_banned_language_linter_contract,identity_crosswalk_hardening_red}.py -q
19 failed, 944 passed
$ git stash pop
```
Full suite in the worktree: **19 failed, 6251 passed, 40 skipped, zero collection errors.**

**Done looks like:** `dg-work.sh DG-NNN && dg-land.sh DG-NNN --dry-run` reports a clean tree and a
green suite on a worktree with no changes in it. Until then every lane must land by hand, which is
precisely the serialisation and test gate `dg-land.sh` exists to enforce.

**Depends on:** nothing. **Blocks:** every open ticket with a worktree — DG-014, 015, 020, 021, 022,
023, 029, 034.

---

**Notes**
`bin/dg-work.sh` currently carries an uncommitted +30/-1 landability change; the 2026-08-23 backup
commit (`1cef3d2`) deliberately left it for its author, and this ticket does not touch it either. It
fixes mechanism 1's *symptom* (the 72 phantom deletions) and reports `landable : 72 phantom deletions
skip-worktree'd`, which reads as success — the exclude half beside it silently does nothing.

Worth deciding explicitly: mechanisms 2-4 are tests asserting things about the **real repo layout**
(gitignore coverage, backup-manifest coverage, crosswalk resolution). They are correct to fail on a
symlink farm. So the fix may not be "make the worktree look real" but "let `dg-land.sh` run its gate
somewhere the layout IS real" — e.g. run the suite in the merge worktree it already creates at
`$WT_ROOT/.land-$TICKET` rather than in the ticket worktree.
