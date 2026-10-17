# Servius — assistant handoff and current work
_Last updated: 2026-10-17. Public repository; operational notes, not live monitoring._

## Read first
This file is the single concise handoff for future homelab work. For emergency storage procedures use [START-HERE](../START-HERE.md); for detailed commands use [Servius operations](../Operations/Servius-Operations-Quickref.md). Older dated snapshots must not override newer verified host output.

## Operating rules / collaboration
- Prefer a single consolidated CLI batch with clear BEGIN/END delimiters; **do not truncate useful output**. Print two blank lines before `===== COPY RESULTS BELOW =====` for easy Termius selection. Read-only diagnostics first, never deploy/restart/delete/rewrite live configs without explicit permission.
- Preserve existing application state and rollback data, particularly TorrentLeech seeds, qBittorrent, Gluetun, ARR databases, and PostgreSQL.
- Keep TrueNAS ix-* app ownership with TrueNAS; Dockhand manages user Compose stacks. Do not deploy duplicate project definitions.
- Write portable, consistent Compose YAML (stable project names, alphabetic services, standardized key order, relative per-stack appdata, .env for host-specific paths); never commit live secrets, full .env, tokens or WireGuard keys.
- Distinguish VERIFIED from PLANNED; make no claims of deployment based only on staged Compose validation. For ongoing Servius work, read this handoff and the relevant runbook **before asking for diagnostics**. Commit verified, non-sensitive milestones to GitHub as work progresses; do not repeat checks whose results are already current. Never commit secrets, and distinguish historical verification from live state.

## Current verified state (Oct 16, 2026)
- Servius TrueNAS SCALE with Docker; appdata root `/mnt/speedpool/appdata/stacks`; pool `speedpool` ONLINE. Media pool `praesidium` DEGRADED, one UNAVAIL RAIDZ2 device (ZFS GUID `3646705126451754359`), no known data errors reported; suspected connection/power path, **not repaired**. Defer risky moves and bulk media changes until recovery/backup checks.
- Dockhand container `ix-dockhand-dockhand-1`, uid/gid 568:568, mounts `/mnt/speedpool/appdata/stacks` to both `/appdata/stacks` and same absolute path. It can read staged compose.yaml and .env; earlier host `runuser -u '#568'` failures were diagnostic false negatives (user lookup), not Dockhand permission failures. Stage env files mode 0640 root:568.
- Production Compose projects currently **unchanged**: `arr-stack` (qBittorrent/Gluetun/Sonarr/Radarr/Prowlarr/FlareSolverr), `arr-stack-secondary` (Bazarr/Cleanuparr/Unpackerr/Houndarr/Jellyseerr/Lidarr), `arr-stack-stats` (Jellystat/DB/Wizarr), plus standalone Whisparr, Byparr, Boxarr. Autobrr has a Compose file but no currently running container.
- Docker shared network `arr-stack_shared`; qBittorrent uses `network_mode: service:gluetun` with AirVPN/WireGuard. qBit forwarded port 37410 previously verified; don't casually change networking. Jellystat PostgreSQL isolated on stats-db.
- Unpackerr **repaired and running**: one configured Sonarr and Radarr each; correct internal URLs `http://sonarr:8989`, `http://radarr:7878`, `/media/downloads`, `delete_orig=false`. Earlier quickref saying repair pending was superseded.
- Cleanuparr actual config at `/mnt/speedpool/appdata/stacks/cleanuparr/cleanuparr/config` despite live Compose owner arr-stack-secondary. Preserve seeding: disable/verify Download Cleaner and Unlinked Downloads before broad changes; old Readarr/Whisparr checks may be stale. ZFS child datasets prevent cross-dataset hardlinks.
- Whisparr is intentionally individually controllable; currently ran on `whisparr_default` only, not shared network. New staged Compose addresses connectivity but is NOT deployed.
- Project staged at `/mnt/speedpool/appdata/stacks/staging/arr-compose-v2` from `servius-arr-compose-staging-v2.zip`. Eight projects each have .env and **8/8 docker compose config --quiet PASS**. Dockhand direct read of compose.yaml/.env PASS. **No production containers or appdata have been migrated.** Do not click Deploy until data moves, backups, owner transitions and rollback are planned.

## Agreed future Dockhand filesystem layout (NOT LIVE)
Four downtime-impact groups:
- `arr-essential`: Gluetun, qBittorrent
- `arr-primary`: FlareSolverr, Prowlarr, Radarr, Sonarr
- `arr-services`: Jellyseerr, Jellystat, PostgreSQL, Wizarr
- `arr-utilities`: Bazarr, Boxarr, Cleanuparr, Houndarr, Unpackerr
Independently managed: `lidarr` (preserve; no longer in main stack), `whisparr` (on-demand), `byparr` (testing), `autobrr` (keep-or-retire decision), `qui` (future experimental).
`standby/` is for retained but **stopped** alternatives; directory location alone does not disable containers. FlareSolverr is important and remains in primary, not standalone. Prefer fewer main stacks, no numerical prefixes.

## October 16–17 verification — backup and migration audit

- `praesidium` still DEGRADED (only 3 of 4 HGST disks visible); replacement SATA power extension omitting 3.3V expected Sunday October 18. No pool repair yet. `speedpool` ONLINE; boot drive actually reports **Samsung 850 EVO 500 GB** (correct older 870 EVO documentation).
- Snapshot tasks verified enabled: `speedpool/appdata` daily/6 weeks (excluding Syncthing), `speedpool/vmdata` daily/1 week; `praesidium/users` daily/4 weeks, `praesidium/appdata` and `praesidium/backups` daily/6 weeks, `speedpool/appdata/syncthing` daily/1 week. New snapshots through Oct 16 verified. These are NOT independent backups.
- Replication task #6 `speedpool` → `praesidium/backups/truenas/speedpool` **disabled**, without associated snapshot tasks; receiving dataset contains no replicated children and only an old May snapshot. MEGA cloud-sync tasks for both appdata paths **disabled**. Both source and destination encrypted AES-256-GCM with separate encryption roots/unlocked; receiver readonly=on. Post-repair proposal: separate appdata/VM replication with review of non-raw encryption/retention. **Not deployed**.
- ZFS AMP zvol `speedpool/vmdata/AMP_vm-c3a7q` has 250G volsize, 484G accounted used = 126G referenced + 118G used by snapshots + 240G used by refreservation; do not infer 484G transfer size or clear snapshots/reservation.
- AMP archive checks on Oct 16 archives for `WuhanVSim20101` and `Storgarr01`: **ZIP CRC PASS** and real game-world data found (Zomboid multiplayer player/vehicle DBs and metagrid, Valheim `Saves/worlds_local` .db/.fwl and chunks). Three daily archives per instance stored on praesidium; **restore still not tested**. Existing [backup runbook](../Operations/Backup-and-Restore.md) already documents VM-side sync, h711nt Syncthing copies, Discord/Uptime Kuma and retention—check that before further diagnostics.
- HomePi Oct 16 systemd backup confirmed success (03:33–03:36), SHA256 MATCH, retention completed and `KUMA_PUSH_OK` **after** successful backup. Push monitoring is installed; Kuma receipt/alert-on-miss and restore/aged-retention tests still unverified. Do not repeat monitor setup.
- ARR production containers and network unchanged; all eight staged `compose.yaml` plus `.env` still present. Main production ARR stacks, Boxarr/Byparr etc. still have original Compose labels; no cutover. Prior staging **8/8 config PASS** from previous session; current audit confirmed presence, *not* a fresh validation. Prefer review of staged-to-live differences and mounts only after checking existing handoff.

## Migration documentation
- [ARR Dockhand migration manifest](../Operations/ARR-Dockhand-Migration-Manifest.md) contains the live-to-staged service map, observed mounts and networks, and staged Houndarr data mount correction (config validation PASS, no deployment).
- [ARR data migration and rollback runbook](../Operations/ARR-Data-Migration-Rollback.md) is a **draft pre-cutover plan** for application-consistent database backups, source-to-target transfer, and scoped rollback. Production has not been migrated; storage and independent backup gates remain blocked.

## Storage capacity audit — October 16–17 (read-only)
- `praesidium` 43.6T raw pool size, 22.5T allocated, 21.1T free (51% CAP, still DEGRADED). RAIDZ2 raw allocated capacity includes parity; not equal to logical file size. `speedpool` 1.81T pool, 545G allocated, 1.28T free (29%, ONLINE).
- `praesidium` ZFS dataset USED ~10.9T, chiefly `appdata/arr_common` 10.4T: TV 4.56T, downloads 2.21T, movies 1.31T, Whisparr 1.30T, anime 772G. Media USED includes only ~52G snapshots across movies (23.7G), TV (20.8G), music (6.12G) and anime (1.18G). **Do not shorten retention merely to save space.**
- `praesidium/backups` 8.82G total: AMP 5.65G, HomePi 3.17G, virtually empty TrueNAS replication. Backups are not the cause of 51% raw allocation.
- Immich storage on **both** pools: `praesidium/appdata/immich` 457G (library 329G, videos 125G); `speedpool/appdata/immich` 202G (data 173G, thumbs 15.8G). These are not proven duplicates. Check actual live mounts before cleanup.
- `speedpool/vmdata` 506G accounted USED, chiefly AMP VM 484G including snapshots 118G and previously measured reservation accounting. Never interpret this as 484G of copyable data.
- User policy: no more than one or two copies of replaceable media, prioritize separate backup of personal files, photos, databases, Docker configs and AMP saves. Downloads/seeding need careful review, no blind deletions. No pool, snapshot, or data modifications performed.

## Next priorities
1. Confirm backup/restorability on single-NVMe speedpool and repair/recover degraded praesidium; avoid moving running PostgreSQL or qBit config data.
2. Compare staged versus active effective Compose configuration and volumes; verify actual .env values and bind ownership locally without printing secrets.
3. Prepare per-stack source-to-target appdata manifests, consistent DB backup, migration ownership cutover, validation and rollback. Preserve old Compose dirs; no double-deploy. Move low-impact apps first, Gluetun/qBit last.
4. Update Dockhand stack metadata to new Compose paths during cutover; ensure appdata stays relative and project labels stable. Existing project names differ, so container recreation is required.
5. Test integrations, health, port mapping, private seeding state, VPN egress, and monitoring after each migration.
6. Longer-term: distinct AirVPN Tailscale exit-node (without altering TrueNAS host route), Kasm/disposable workstation, monitoring; HomePi residential exit existing but phone test still pending.

## Memory / documentation policy
This is a **repo handoff**, not the model's hidden prompt or automatic memory. It is intentionally readable by any repository visitor. Never insert confidential material, private personal information, raw user logs containing tokens, or undocumented assumptions. Update status after verification; date changes, link deep detail instead of duplicating massive reports.

## Authentik Dockhand Compose save — verified October 16–17

Dockhand (UID/GID 568:568) had an EACCES on `/appdata/authentik/compose.yml`, mounted from `/mnt/speedpool/appdata/authentik/compose.yml`. Host diagnosis: parent appdata already 568:568/0775; Authentik directory and compose file were root:root with modes 0755 and 0644. Operator changed **only** Authentik top-level directory to root:apps/0775 and compose file to root:apps/0664, keeping root ownership and not applying recursive chmod/chown. Operator confirmed **Dockhand Edit → Save now succeeds**. Authentik remained running with 3 containers in Dockhand screenshot; no deployment/restart implied by the save verification. Do not repeat this permission diagnostic unless it regresses.

## Kuma Container Sync temporary-monitor filter — verified October 16–17

Production Compose service `kuma-sync` runs container `kuma-container-sync` from `ghcr.io/benrhughes/kuma-container-sync:latest`. Original `/app/monitor.py` copied to `/mnt/speedpool/appdata/kuma-sync/monitor.upstream.py`; patched source at `/mnt/speedpool/appdata/kuma-sync/monitor.py`. Compose backup: `/mnt/speedpool/appdata/kuma-sync/compose.yaml.pre-filter.bak`. Compose mounts `./monitor.py:/app/monitor.py:ro` (verified RW=false) and `docker compose config --quiet` passed. The Compose service alone was recreated.

Local patch filters initial Docker discovery: excludes `com.docker.compose.oneoff=true`, `ix-*` names containing `-permissions-` or `-init-`, nonrunning containers, and running containers younger than 600 seconds. Does not delete old Kuma monitors. Logs from first cycle: self-container waiting to mature; ix-filebrowser init and permissions jobs excluded, Jellyfin permissions excluded, Immich permissions excluded, WinApps and Immich upgrade stopped candidates excluded. 43 eligible running containers, authenticated successfully, notification `Servius Discord Alert` found, 71 existing monitors, `Sync completed successfully`, then waits 300 seconds. **Active and verified.** Limit: unusually long-running temporary containers without one-off/init labels may still be included; changed upstream image behavior requires reviewing local patch before updates. Avoid copying full live compose/.env into public repo.

## Oct 17 TrueNAS scheduler snapshot

Root cron **enabled** `Servius ZFS Health - Kuma`: `/bin/bash /mnt/speedpool/appdata/scripts/zfs-kuma-health.sh`, `*/5 * * * *` (screenshot). Previous manual script/heartbeat verified; first unattended execution and Discord alert receipt **not** verified. Other observed cron: Authentik nightly backup 03:30; AMP retention 07:00; old ix-apps snapshot cron disabled. Both `speedpool` and degraded `praesidium` have enabled Sunday midnight scrub schedules. Six periodic ZFS snapshot tasks enabled and shown PENDING in GUI; disabled MEGA and speedpool replication persist; all-disk short SMART Sunday and long SMART monthly day1 configured. See [Backup and Restore](../Operations/Backup-and-Restore.md#truenas-gui-task-inventory--october-17-2026-screenshots-configuration-only) for statuses and caveats.
