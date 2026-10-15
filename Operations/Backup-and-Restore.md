# Backup and restore · Servius

> **Evidence date:** October 14, 2026; **restore confidence: partial**. A successful copy is not proof that the application can be restored. Check current task results before trusting a backup.

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

**See [HomePi backup and restoration runbook](HomePi-Backup-and-Restore.md) for exact paths, safe checks, exclusions and follow-ups.** Servius Uptime Kuma WebUI Push monitoring remains pending.

## Routine checklist

- [ ] Verify TrueNAS alerts and most recent successful data protection jobs.
- [ ] Confirm **each AMP instance** created a fresh ZIP and Syncthing received it.
- [ ] Check backup destination has sufficient free space.
- [ ] Validate snapshots and retention settings before deleting anything.
- [ ] Perform a non-destructive test restore (application, world, and personal file).
- [ ] Export TrueNAS settings with secret seed after meaningful changes; keep them out of GitHub.
- [ ] Record restored-from date, resulting state and any problems in a new [incident note](../Templates/Incident.md).
