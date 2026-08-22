# DG-035 — The capture chain silently does not run unless David is logged in

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** 2026-08-22 migration incident, measured live on the new MacBook Pro (M5 Pro). Scoped
as a ticket on David's instruction the same day: *"Scope it as a new ticket, decide after SR-11."*

**Problem:** every producer in the daily chain is a **LaunchAgent** (`gui/501`). LaunchAgents exist
only inside a logged-in GUI session. If the Mac is booted but nobody has logged in, the jobs are not
merely delayed — they do not exist, and launchd **does not replay** a `StartCalendarInterval` slot
that elapsed while the user was logged out. Nothing anywhere records that the slot was skipped.

On 2026-08-22 the machine booted at **09:04:39** and the console login landed at **10:48:52**.
Thirteen scheduled job slots fell inside that window and every one of them silently did not run,
including the 10:15 offsite backup — the product's disaster floor. The gap was found only because a
human went looking; no log, marker, exit code or health surface recorded it.

**How we know:** measured, not inferred.
- `sysctl -n kern.boottime` → `Sat Aug 22 09:04:39 2026`; `who` → `davidleess console Aug 22 10:48`.
- After login, `launchctl print gui/501/com.davidleess.dynasty-what-changed-report` (09:45 slot) and
  `...dynasty-backup-irreplaceable` (10:15 slot) both read `runs = 0`,
  `last exit code = (never exited)` — they never attempted.
- The clean control: `dynasty-model-pvo-refresh` holds **both** a missed 09:30 slot and a live 11:30
  slot. It first spawned at **11:30:00.467**. The elapsed 09:30 slot was never made up.
- macOS 26.5.1 (25F80). This is distinct from missed-while-**asleep**, which launchd does handle.

**Why the obvious fix does not fix it.** Restoring the 6:00 AM wake
(`pmset repeat wakeorpoweron`) was the first instinct and it is **not sufficient**: a scheduled wake
powers the machine on, it does **not** create a login session. Today the machine was awake and
running from 09:04 and the jobs still did not fire. The wake is worth restoring for the
sleep case; it does not address this one.

**Done looks like:** either (a) the season-critical producers no longer depend on a console login —
the standard route is **LaunchDaemons** (`/Library/LaunchDaemons`, root-owned, run from boot with no
session), which is a Tier 2 change to how every job is scheduled and overlaps SR-09's chain rewire;
or (b) the dependency is accepted deliberately and made **loud** — a boot-to-login gap that swallows
a slot must produce an alert, not silence. Option (b) is strictly cheaper and is the minimum bar.

**Depends on:** nothing. **Overlaps:** SR-09 (chain rewire) and SR-11 (the alert). SR-11 must be
able to report a job that *never attempted* — a class invisible to logs, markers and exit codes,
because a job that never spawned writes none of them.

---

**Notes**
Not a migration artifact. Migration made it visible by forcing a boot-and-login gap that a
normally-always-logged-in laptop rarely produces. The exposure is proportional to how often the
machine reboots without a prompt login — OS updates, crashes, power loss — none of which are rare
across a 17-week season, and all of which are silent today.

Related but separate, from the same incident: three jobs entered launchd's **penalty box** after
failing to spawn, and stayed there. Sticky, invisible, and `kickstart` does not clear it — only
`bootout` + `bootstrap`. Cleared 2026-08-22 on David's word and verified by an unattended 14:00
firing. That failure mode also writes nothing to disk: it lives only in launchd's in-memory state
and dies at reboot, taking the evidence with it.
