# Homelab changelog

Entries distinguish **verified from terminal output**, **reported configuration**, and **pending verification**. Dated snapshots are not live status.

## 2026-10-15 12:43 — Servius read-only health audit (iOS)

- **P0:** `sudo zpool status -x` confirmed `praesidium` **DEGRADED**, RAIDZ2 with one `UNAVAIL` member (`3646705126451754359`); ZFS reports label missing/invalid. Three other members ONLINE, no known data errors. Last displayed scrub Oct 11: repaired 0B, 0 errors. **Do not clear errors, replace member, scrub on demand or change pool until on-site identification and recovery plan.**
- `df -h /mnt/speedpool /mnt/praesidium` showed mounted dataset usage 1% at those paths, **not** definitive recursive pool usage; inspect `zpool list` and `zfs list` later.
- `ipa-epn.service` failed (IPA Expiring Password Notification); determine whether installed/needed before clearing or disabling.
- `kuma-container-sync` running but unhealthy; logs pending.
- Exited-0 helper containers for FileBrowser Quantum, Jellyfin and Immich are not necessarily faults. `WinApps` and `whisparr` exited 0; `parrot` exited 137; classify intended retirement before deleting.
- Failed job history contains TrueNAS app upgrades with validation errors (`run_as.user`, `network.web_port`), prior FileStash app creation failures, and recurring cron failure in a `zfs snapshot -r speedpool/ix-apps@auto-...` script; investigate live scheduled task ownership before editing.
- **No destructive repair, service restart, pool mutation or installation performed in this audit.** Continue only safe low-impact fixes from iOS; major repairs postponed until physically home.

## 2026-10-15 — Additional read-only housekeeping audit

- Confirmed one storage pool remains degraded; physical repair deferred until on site.
- Verified other storage pools are online, with no immediate capacity concern.
- Identified a monitoring synchronization configuration mismatch; no changes made.
- Recorded one failed notification service and several stopped containers for later classification.
- No services restarted, data deleted, or storage operations performed.

## 2026-10-15 — HomePi backup reliability, planning and documentation

### Verified
- Diagnosed a long-running SQLite online backup of `/etc/pihole/pihole-FTL.db` (Pi-hole query-history database). It repeatedly processed far more data than source size. Removed FTL *history* DB from daily snapshot targets; retained Pi-hole configuration and `gravity.db`. The history database will **not** be recoverable from these archives.
- HomePi `/opt/homepi-backup/daily.sh` completed successfully, including SQLite `quick_check` of `gravity.db`, Dockhand DB, and Uptime Kuma `kuma.db`; remote SHA-256 checksum matched.
- Initial successful archive `homepi-apps-20261015-113145.tar.gz`, approximately 121 MiB, confirmed on Servius. `gzip -t` passed and archive listing included Uptime Kuma and Pi-hole files.
- Configured and enabled `homepi-backup.timer` (~03:30 Mountain time, up to five-minute randomized delay, persistent missed-run), and `homepi-backup.service` (300-second timeout). Timer reported enabled/active.
- Second full systemd-integrated backup succeeded: `homepi-apps-20261015-114743.tar.gz`, ~121 MiB, SHA-256 match, `Result=success`, exit 0. Remote retention executed in dry-run mode and preserved both archives.
- Created `/opt/homepi-backup/retention.py` with default dry-run / explicit `--apply` deletion, exact remote directory guard, newest and initial restore-point protections. Updated `daily.sh` to invoke retention with `--apply` after checksum verification; Bash syntax validated. **An actual deletion of expired archives has not been tested.**
- Confirmed target account's working directory on Servius: `/mnt/praesidium/backups/homepi/homepi-backup`. Remote owner `homepi-backup`; SSH key and destination are read from private `/home/dietpi/homepi-first-backup.sh`.
- Confirmed existing Servius Uptime Kuma container `ix-uptime-kuma-uptime-kuma-1`, version 2.5.6, host port 31050, returns HTTP 302 on base URL (expected redirect). A local host Python venv failed because `ensurepip` / `python3.11-venv` was missing; no host package installation performed.
- Chose existing Servius Uptime Kuma **WebUI**, not CLI, for future HomePi Push monitor creation; no monitoring instance should be added on HomePi.
- Created and linked prioritized `ROADMAP.md`, with security, backups, stability, proposed application deployments and older ideas.

### Important follow-ups
- Create `HomePi - Daily Backup` Push monitor in existing Servius Uptime Kuma; configure alerts, keep secret URL out of Git; send heartbeat only after verification **and** retention success.
- Test first overnight unattended run, failure detection, remote retention on disposable expired archives and end-to-end restore.
- Review five-minute whole-job timeout for growing archives / slow transfers; inspect policy for four age-based weekly slots.
- Confirm pool health, offsite cloud-sync failure, independent speedpool backup and restore tests.

## 2026-10-14 — Servius operations and inventory reconciliation

### Reported and documented
- Updated service inventory and operations handbook from operator-provided Oct 14 Docker captures: 49 containers (41 running, 8 exited). These counts are **historical** and may change.
- Confirmed Docker/TrueNAS ownership distinctions. Dockhand is a TrueNAS-managed `ix-*` app, while user-created Compose projects live under `/mnt/speedpool/appdata/stacks/`.
- Verified ARR stack Compose paths `/mnt/speedpool/appdata/stacks/{arr-stack,arr-stack-secondary,arr-stack-stats}/compose.yaml`. Corresponding `.env` files are sensitive and excluded from Git.
- Recorded Dockhand v1.0.53, Authentik 2026.8.3, and application inventory as observed; don't assume versions remain current.
- Captured AMP backup design, Syncthing replication to h711nt, and SHA-256 verification for Valheim and Project Zomboid archives; full game restore untested.
- Recorded storage warnings: `speedpool` is a single NVMe; `praesidium` had a historical degraded/cabling event. Verify live status before changes.
- Identified monitoring gap `kuma-container-sync` historically unhealthy; failed TrueNAS cloud-sync task needs investigation.
- Confirmed ongoing direction: Dockhand consolidation, minimizing public exposure, backup-first changes, controlled updates rather than indiscriminate auto-upgrades.

## Status conventions

- **Verified** = supported by specific output or completed test on the stated date.
- **Configured** = file/task exists or command accepted; full behavior may not have been tested.
- **Reported** = observed in previous notes or screenshots; confirm current state.
- **Pending** = not complete; see [roadmap](ROADMAP.md).
