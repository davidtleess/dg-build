# DG-036 — A failed backup can leave the previous run's `completed` marker standing

**Layer:** 1  ·  **State:** todo  ·  **Lane:** —  ·  **DG 3.0**
**Source:** adversarial verification during DG-034, 2026-08-23. Found by attacking a claim about the
marker's failure taxonomy, not by looking for this.

**Problem:** `scripts/backup_irreplaceable_data.py:393-401` writes the marker inside a `try`, and on
`OSError` appends `marker_write_failed` and sets `status = "failed"` — but those mutations land on the
**returned dict only**. `json.dumps(marker, ...)` at `:396` is fully evaluated before `write_text` is
called, so the serialized bytes are frozen before `:399` mutates the list. Nothing about the failure
reaches disk.

If a previous run already wrote a `completed` marker and a later run fails **and** cannot overwrite it,
the on-disk marker still reads `status: completed`, `sha256_verified: true` — describing a run that is
not the last one attempted. Every consumer of `/api/system/capture-health` is told the backup
succeeded.

The 26-hour staleness rule eventually catches it, because the stale marker keeps the *old* run's
`finished_at`. Inside that window it is invisible.

**How we know:** driven directly at `run_backup` with the marker path made a directory, so the write
raises `IsADirectoryError`:
```
C  marker path is a directory (OSError on write)
   RETURNED status= failed    failures= [..., 'marker_write_failed']
   ONDISK   <not a file>

D  stale completed marker + failed write
   RETURNED status= failed    failures= ['missing_optional:...', 'auth_unavailable', 'marker_write_failed']
   ONDISK   status= completed failures= ['missing_optional:...', 'missing_optional:...']
```
Scenario D is the live hazard: the returned run failed, the on-disk marker says completed.

**Done looks like:** a run that cannot write its marker cannot leave a previous success standing as the
current truth. Either the marker write is made atomic-with-failure (write a failure marker to a path
that must succeed, or fsync+rename so a partial write cannot masquerade), or the reader learns to
compare the marker's `run_id` against the run it should have seen. A test asserts that a failed run
following a successful one never leaves `status: completed` readable.

**Depends on:** nothing. **Related:** DG-034 (the two blind spots in the reader), DG-033 (a producer's
own terminal status not being read).

---

**Notes**
Two smaller things found in the same pass, neither filed separately:
- `:400-401` reset `status` and `sha256_verified` on the returned dict but never reset `run_prefix`
  (set at `:385`), so a returned `failed` run carries a non-null `run_prefix`.
- The project's own ledger already recorded the adjacent race:
  `docs/agent-ledger/2026-08-01.md:471-473` — *"both jobs write the same local
  `backup_status_latest.json` without a lock. A later failing run can overwrite a prior verified
  marker."* Unlocked concurrent writers remain unaddressed.

Structural context worth carrying into any fix: `inspect_backup_marker` never reads `schema_version`
(zero references in `app/api/routes/system_capture_health_models.py`), and the marker is gitignored
(`.gitignore:206`). The consumer therefore cannot treat the producer's vocabulary as an invariant —
which is why DG-034 shipped a **fail-closed** allowlist rather than a known-bad list. The elegant
long-term fix is producer-side: a `backup_status.v2` marker splitting `failures` into `tolerated[]`
and `blocking[]`, so the component that knows which is which is the one that says so.
