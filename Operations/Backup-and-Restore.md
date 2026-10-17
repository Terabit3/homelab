# Backup and restore · Servius

 > **Evidence date:** October 17, 2026; **restore confidence: partial**. A successful copy is not proof that the application can be restored. Check current task results before trusting a backup.

## Before doing anything destructive

1. Determine **what will be lost** (files, database, application settings, VM disks, or the TrueNAS OS).
2. Identify the newest **known-good independent backup** and whether it includes the required data.
3. Check *last success* and integrity, not merely that a task exists.
4. Work out **where to restore** and the exact **rollback** plan. Prefer a test/temporary target.
5. If there is only one surviving copy, stop and avoid modifying it.

## What is currently recorded

| Asset | Recorded protection | Confidence / gap |
|---|---|---|
| AMP Valheim & Project Zomboid ZIPs | AMP → `/mnt/praesidium/backups/amp/` → h711nt Syncthing `E:\TrueNAS Syncthing\backups\amp` | Byte-identical SHA-256 copies verified Oct 14; **full restore not tested** |
| ARR / Homepage deployment configs | Private Windows vault `E:\Homelab-Private\Servius\` | Specific copied files hash-verified; **application database and full service restores not proven** |
| `speedpool` NVMe appdata and VM | Depends on separate datasets and backup jobs | **Do not assume Syncthing of live database files provides a consistent restore** |
| TrueNAS boot configuration | Separate private export needed | **Current configuration export and seed availability not verified** |
| `praesidium` bulk data | Check snapshots, backup replication and cloud tasks | **Backup completeness and external recovery not verified** |
| MEGA/offsite | Historical planning mention | **Do not mark operational without live evidence** |

## AMP games

**Runs in:** Debian AMP VM (not TrueNAS Docker).

| Item | Documented location |
|---|---|
| AMP VM sync script | `/usr/local/sbin/amp-backup-sync.sh` |
| VM notification wrapper | `/usr/local/sbin/amp-backup-notify.sh` |
| VM cron | `/etc/cron.d/amp-backup-sync` (06:00 Mountain time as configured) |
| Servius archives | `/mnt/praesidium/backups/amp/<instance>/` |
| Cleanup script | `/mnt/speedpool/appdata/scripts/amp-backup-retention.sh` |
| Cleanup | TrueNAS task around 07:00, 14-day policy preserving newest per instance; **first aged deletion not verified** |
| h711nt copy | `E:\TrueNAS Syncthing\backups\amp` via Receive Only Syncthing |
| Alerts | Discord `#backups`, Uptime Kuma `AMP Daily Backups` Push — **keep URLs secret** |

**Critical limitation:** the VM sync script skips instances with no recent ZIP. A per-instance backup may silently stop without the overall sync appearing broken. Check both intended instances individually.

**Test restoration (planned, not an executable instruction yet):** Use a separate temporary instance or safe test environment. Restore a copied ZIP, confirm world loads, expected characters/inventory exist, and mods/versions match. **Do not overwrite the running production save to test a backup.**

## Restore a deleted file

Use [TrueNAS snapshot recovery](TrueNAS-Troubleshooting-Runbook.md#7-recover-an-accidentally-deleted-or-modified-file). Prefer copying one file out of a snapshot or versioned backup over whole-dataset rollback.

## TrueNAS OS boot failure

Use [boot recovery](TrueNAS-Troubleshooting-Runbook.md#8-truenas-wont-boot--boot-ssd-failed). A config export with **secret seed** helps restore TrueNAS settings; it does **not** back up the data pools. The planned Kioxia boot mirror is **not configured**.

## HomePi — October 15 verified backups

The HomePi systemd backup job runs around 03:30 local time and writes to `/mnt/praesidium/backups/homepi/homepi-backup/` on Servius. Two approximately 121 MiB archives passed remote SHA-256 verification, and the first passed gzip integrity testing. Selected SQLite databases were checked; live Pi-hole FTL query history is **excluded** because its snapshot repeatedly copied far more data than its size. Retention is integrated, but actual aged-file deletion and restoration have **not** been tested.

**See [HomePi backup and restoration runbook](HomePi-Backup-and-Restore.md) for exact paths, safe checks, exclusions and follow-ups.** October 16 logs confirm a HomePi success-only heartbeat (`KUMA_PUSH_OK`) after transfer checksum and retention. Verify its receipt and missed-run notification in Servius Uptime Kuma before declaring alerting fully tested.

## October 16–17 follow-up audit

- **AMP:** Two latest ZIP files (`WuhanVSim20101`, `Storgarr01`) passed ZIP CRC. Their archive indexes include the actual game-world components (Zomboid multiplayer database/metagrid and Valheim world .db/.fwl/chunks). There are three daily ZIPs per instance on `praesidium`; recovery by restoring to a clean server remains untested. Keep previously documented **VM sync → Servius → h711nt Syncthing**, retention job, Discord and Kuma architecture; do not replace it with a duplicate backup flow.
- **HomePi:** October 16 unattended service exited 0 after 03:33–03:36 run, SHA256 MATCH, retention completed and `KUMA_PUSH_OK`. No need to create the Push configuration again. Still verify monitoring receipt and missed-heartbeat notifications, retention aging and independent test restore.
- **ZFS:** Source snapshots verified on Oct 16: appdata daily/6 weeks and VM data daily/1 week. Encrypted source `speedpool` has no current independent verified pool-level replica: task #6 disabled with no snapshot associations, `praesidium/backups/truenas/speedpool` contains only an old May snapshot, and MEGA tasks are disabled.
- **Plan only (no changes made):** After degraded `praesidium` is repaired, review and configure separate appdata and VM replication destinations, encryption/retention, application-consistent database backups and a test restore. Do not run large transfers while degraded.

## Media retention policy and actual capacity — October 16–17

The operator wants replaceable media (movies, TV, common music) kept to **one primary copy and at most one additional copy** where justified. A RAIDZ2 vdev is fault tolerance, **not** an independent backup. Preserve stronger independent/offsite protection for photos, DJI originals, personal documents, app databases/config, Immich and game saves. Do not bulk-replicate the replaceable media library merely because of existing replication plans.

Read-only TrueNAS measurements: `praesidium` 43.6T raw, 22.5T ALLOC (51%), with logical ZFS `USED` ~10.9T. `praesidium/appdata/arr_common` ~10.4T: TV 4.56T, downloads 2.21T, movies 1.31T, Whisparr 1.30T, anime 772G. Media snapshot charges for TV (20.8G), movies (23.7G), anime (1.18G) and music (6.12G) are small. Backup datasets **only 8.82G** total (AMP 5.65G, HomePi 3.17G); TrueNAS replication receiving dataset remains virtually empty. Raw RAIDZ2 allocation includes parity and is not directly comparable to logical dataset usage. No snapshot or backup retention was changed.

`praesidium/appdata/immich` 457G and `speedpool/appdata/immich` 202G both exist; **do not infer redundant content** without live mount and integrity review. Avoid dangerous cleanup while `praesidium` is DEGRADED. Separate actual backups from same-pool snapshots and verify independent restores.

## Routine checklist

- [ ] Verify TrueNAS alerts and most recent successful data protection jobs.
- [ ] Confirm **each AMP instance** created a fresh ZIP and Syncthing received it.
- [ ] Check backup destination has sufficient free space.
- [ ] Validate snapshots and retention settings before deleting anything.
- [ ] Perform a non-destructive test restore (application, world, and personal file).
- [ ] Export TrueNAS settings with secret seed after meaningful changes; keep them out of GitHub.
- [ ] Record restored-from date, resulting state and any problems in a new [incident note](../Templates/Incident.md).

## TrueNAS GUI task inventory — October 17, 2026 (screenshots; configuration only)

Captured via TrueNAS Cron Jobs, Data Protection and Periodic S.M.A.R.T. Tests screens. These are **UI configuration/status observations, not proof of execution or successful backups**.

| Category | Visible configuration | Interpretation / follow-up |
|---|---|---|
| Cron: Servius ZFS Health - Kuma | root; `/bin/bash /mnt/speedpool/appdata/scripts/zfs-kuma-health.sh`; `*/5 * * * *`; Enabled Yes | **Scheduled** every five minutes. Previous one-time manual live Push succeeded and reported `praesidium=DEGRADED`; unattended execution and Discord receipt not yet verified |
| Cron: Authentik nightly backup | root; `/mnt/speedpool/appdata/authentik/backup.sh`; `30 3 * * *`; Enabled Yes | Script configured; last successful archive/restore not established by screenshot |
| Cron: AMP Backup Retention | root; starts `/bin/bash /mnt/speedpool/appdata/scripts/amp-...`; `0 7 * * *`; Enabled Yes | Retention scheduled; aged deletion still untested |
| Cron: historical ix-apps snapshot | root; command beginning `zfs snapshot -r speedpool/ix-apps@auto-...`; Disabled | Leave disabled; native ZFS snapshot tasks operate separately |
| Scrub tasks | `speedpool` and `praesidium` enabled; Sunday 00:00 shown | Confirm maintenance appropriateness while `praesidium` remains DEGRADED; **do not manually start a scrub** during recovery |
| Cloud sync | Two MEGA tasks disabled; one shows historical SUCCESS (5 months ago), the other historical FAILED (9 months ago) | Neither is a current offsite backup |
| Periodic snapshots | Six enabled: `praesidium/users` 4wk daily 00:00; `speedpool/appdata` 6wk daily 00:00; `speedpool/vmdata` 1wk daily 00:00; `praesidium/appdata` 6wk daily 00:00; `praesidium/backups` 6wk daily 00:00; `speedpool/appdata/syncthing` 1wk daily 00:15 | GUI shows PENDING / Last Run N/A at capture; **do not infer failures** solely from PENDING. Earlier Oct16 actual snapshots were verified |
| Replication | `speedpool` to `praesidium/backups/...` disabled; No snapshots sent yet, PENDING / Last Run N/A | No current independent speedpool replication |
| Periodic SMART | All Disks SHORT scheduled Sunday 00:00; All Disks LONG scheduled monthly on day 1 at 00:00 (other monthly scheduling parameters truncated in screenshot) | Jobs listed; their enablement/result and actual test history should be verified independently |

**Remaining:** Confirm TrueNAS cron job executes on schedule via middleware/query or logs; check Kuma #93 gets recurrent DOWN heartbeat with the actual degraded-state message, and confirm Discord routing. Only then mark fully operational. These screenshots do not document full exact commands for truncated table rows or evidence of completed test runs.
