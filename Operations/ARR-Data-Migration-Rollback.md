# ARR data migration and rollback — pre-cutover runbook

> **PLANNED, NOT EXECUTED.** Prepared from verified Servius mount and network inventories on October 16–17, 2026. This document is not authorization to stop containers, copy database files, move datasets or deploy staged stacks. Read [ARR migration manifest](ARR-Dockhand-Migration-Manifest.md) and [Servius handoff](../Assistant-Notes/Servius.md) first.

## Hard prerequisites

- [ ] Restore `praesidium` from DEGRADED to healthy state; verify expected HGST device and resilver status before proceeding. Do not assume missing member is physically defective.
- [ ] Establish and verify independent recoverable backups of `speedpool` Docker application data and database contents. Snapshots on the same single NVMe are not independent copies. AMP/HomePi ZIP checks do not satisfy ARR recovery.
- [ ] Export/secure production Compose definitions, private environment values, credentials, relevant volume metadata and TrueNAS configuration *outside public GitHub*. Keep existing Compose definitions available for rollback.
- [ ] Resolve permanent **target Compose directory** before interpreting relative bind paths. Staging presently resides beneath `stacks/staging/arr-compose-v2/<project>`. Do not deploy from staging and later relocate without accounting for moved mount bases.
- [ ] Validate available space, ZFS boundaries, ownership, full existing mount list, each service's safe-stop method, restart policy, and rollback recovery point. Verify a representative offline restore.
- [ ] Review owner split: original `arr-stack-secondary` will be split across utilities/services/Lidarr; old project-level `down` would affect unrelated containers. **No blanket `docker compose down` on split projects.**

## Source-to-destination rule

Use [verified live source paths](ARR-Dockhand-Migration-Manifest.md#verified-live-mounts-oct-17-2026). Proposed staged project-relative paths are:

| New project | Services | Planned relative target directories | Backup / cutover concerns |
|---|---|---|---|
| `arr-utilities` | Bazarr, Boxarr, Cleanuparr, Houndarr, Unpackerr | `./bazarr/config`, `./boxarr/config`, `./cleanuparr/config`, `./houndarr`, `./houndarr-data`, `./unpackerr/config` | Houndarr master key and live SQLite WAL; Cleanuparr destructive automation; Boxarr current standalone owner |
| `arr-primary` | FlareSolverr, Prowlarr, Radarr, Sonarr | `./prowlarr`, `./radarr`, `./sonarr`; FlareSolverr no staged volume | SQLite state; app API credentials; review empty FlareSolverr old volume |
| `arr-services` | Jellyseerr, Jellystat, Jellystat PostgreSQL, Wizarr | `./jellyseerr`, `./jellystat/backup-data`, `./jellystat-db/data`, `./wizarr/data` | Preserve Jellyseerr DB/login; Jellystat PostgreSQL isolation, versions and consistency |
| `lidarr` | Lidarr | `./lidarr/config`, `./lidarr/music` | Separate music bind and media path; keep installed/stopped decision separate |
| `arr-essential` | Gluetun, qBittorrent | `./gluetun`, `./qbit` | LAST: preserve AirVPN WireGuard, qBit torrents, save paths, credentials, private tracker state, forwarded port |
| `byparr`, `whisparr`, `autobrr` | Independently managed | Consult staged Compose files | Preserve Byparr behavior; Whisparr intentionally stopped; Autobrr not running |

The effective `MEDIA_PATH` in five staged projects matches live `/mnt/praesidium/appdata/arr_common`; do **not** copy that media tree as part of config migration. Preserve path/container destination `/media` and verify hardlink behavior across ZFS boundaries before changing media placement.

## Database-specific preservation strategy

**All backups go to a separate, access-controlled location; never commit backup artifacts, `.env`, API keys or database encryption keys to this repository.**

1. **SQLite users (Prowlarr, Sonarr, Radarr, likely Bazarr/Lidarr/Jellyseerr and other application-specific DBs):** verify each actual DB engine and database location before the cutover. Prefer the app's supported built-in backup/export or SQLite online backup API while it is running; for final migration stop only the affected service and copy its full config tree with WAL sidecars if present. Validate DB integrity and restore to a test target. Do not assume all services use SQLite or that copying `.db` alone is safe.
2. **Houndarr:** verified live Docker volume `/data` contains `houndarr.db`, `houndarr.db-wal`, `houndarr.db-shm`, and `houndarr.masterkey` (UID/GID 568:568; masterkey mode 0600). Staged `arr-utilities` Compose was modified to include `./houndarr-data:/data`, alongside `./houndarr:/config`; validation passed. At cutover stop Houndarr in a scoped way, transfer the full `/data` state while stopped, preserve numeric IDs/modes, validate key and DB presence before starting. Do **not** display or publish master-key contents.
3. **Jellystat PostgreSQL:** the source database bind is `/mnt/speedpool/appdata/stacks/arr-stack-stats/jellystat-db/data`. Determine server version and database list privately. Prepare a logical `pg_dump`/appropriate PostgreSQL backup with restore test; ensure target is same compatible major version (or plan a supported upgrade). Stop Jellystat DB writer(s) and DB during final physical bind data migration, or initialize a clean DB from logical backup. Never `rsync` a running PostgreSQL data directory as the only recovery method. Preserve DB-only isolation (`stats_db` network in staged arr-services).
4. **qBittorrent:** preserve full `arr-stack/qbit` directory with session/torrent metadata, trackers and paths. Stop qBit only during a coordinated Gluetun cutover after final backups and a test plan for VPN-only egress. Never allow torrent traffic outside VPN, alter state, or trigger a bulk recheck/import without operator review.
5. **Other app directories:** inspect what is persistent vs generated cache, but default to preserving all application data and ownership during cutover. FlareSolverr's existing Docker-managed `/config` volume contained **zero regular files** when inspected. No claim that application behavior is guaranteed without it.

## Controlled cutover plan — future, not executable commands

1. **Preflight:** Confirm RAID health and independent backup recovery, images/app versions, complete source-to-target manifest, free space, effective Compose settings, destination ownership, Dockhand ownership, host ports, shared networks, and service dependencies. Capture a safe baseline of app health and key business functions.
2. **Back up:** Create application-consistent DB exports / app backups, preserve secret material privately, and verify copied archives and test recovery. Record artifact location and timestamps in the private operations log.
3. **Scope a migration unit:** Because services currently share Compose project ownership, remove/recreate *only the selected service(s)*. Explicitly map the old container owner and network so no second container with the same name or port starts before the old one is stopped. Never use a broad old-stack teardown.
4. **Quiesce service:** Disable risky automated cleanup actions as needed (especially Cleanuparr); stop only affected writer containers during the final file transfer; prevent automatic Docker restart races.
5. **Copy to final target:** Make a byte-preserving, ownership-preserving copy; do **not** move/delete original source. Do not publish paths containing secrets or keys. Confirm counts, checksums where suitable, permissions, symlink behavior, and no unexpected subdataset crossings.
6. **Start under new owner:** Deploy corresponding new Compose service/project in Dockhand only when no old duplicate is running. Ensure volume source path is correct (not accidentally under staging). Observe logs without dumping secrets.
7. **Verify:** Confirm app DB starts; settings/history present; ARR endpoints (Sonarr 8989, Radarr 7878, Prowlarr 9696), downloader connectivity, Unpackerr, Jellyseerr login, Houndarr history/key functionality, Jellystat DB access and network isolation, monitoring, download locations and links. For Gluetun/qBit, separately verify VPN egress, WebUI 8080, published 6881 TCP/UDP, AirVPN port forwarding and private tracker seeding. Only resume cleanup automation after checks.
8. **Commit milestone:** Record verified new state in this repo and private recovery details separately. Keep old source directories and rollback images/Compose until a stable observation window and tested recovery.

## Rollback decision tree

- **Before any production stop:** Abort preparation; production remains unchanged.
- **Copy/verification failed before starting new service:** Keep old container owner and source directories intact; do not deploy the new project. Fix plan and retry another day.
- **New service does not start / DB corrupt / missing settings / network integration fails:** Stop *only* the new scoped service, preserve failed destination for analysis, restore old Compose owner and original source bind path, then confirm baseline settings. Do not write new-version DB files into old source.
- **Database schema migrated by a new image:** Rolling back the image against upgraded DB files may be unsafe. Restore the compatible **pre-cutover** logical export or directory backup to an isolated target; do not blindly downgrade a modified DB.
- **qBittorrent VPN fail-open or missing torrents:** Stop qBit immediately, avoid non-VPN traffic, revert only the essential project using untouched original qBit and Gluetun state, verify VPN/trackers before restarting.
- **Houndarr loses access to encrypted state:** Stop newly deployed Houndarr, verify the matching preserved master key and SQLite backup together; revert to old volume and image if necessary. Never regenerate key against existing encrypted data as a workaround.
- **Wider storage fault / pool health regression:** Stop cutover. Prioritize storage recovery with [START-HERE](../START-HERE.md) and existing verified backups.

## Capacity and media protection policy

The operator does not want multiple historical independent copies of replaceable media. A primary copy and at most one justified secondary copy are the target; limit backup scope to irreplaceable personal/Immich content and application state, not all movies and TV. The read-only `praesidium` audit showed 51% *raw* pool allocation (22.5T of 43.6T), about 10.4T dataset usage in the shared ARR media hierarchy, and only 8.82G in backup datasets. The 2.21T downloads dataset must be preserved for seeding while its utility is reviewed. These values do not justify any deletion during the degraded state.

## Still unknown / must verify before approval

- [ ] Final new permanent stack path and resolved relative volume sources.
- [ ] Per-service database versions/types, backup procedure, verified restore and app version/image pinning.
- [ ] Effective staged Compose config vs live besides the already compared mounts/networks; labels, permissions, healthchecks, security settings, resource reservations and volume nesting.
- [ ] Actual free space/copy scope and filesystem ownership/ACLs of all old and prospective target directories.
- [ ] Safe owner transfer mechanics in Dockhand for old projects with no config file reported by `docker compose ls`.
- [ ] Independent backup for `speedpool` and healthy `praesidium`.
- [ ] Operator-approved maintenance window, rollback criteria, and post-cutover monitoring.
