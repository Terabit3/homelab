# ARR Dockhand migration manifest

> **Status: DRAFT / PRE-CUTOVER ONLY**, Oct 17 2026. Based on [Servius handoff](../../Assistant-Notes/Servius.md) and observed Oct 16 active Compose inventory. **No Docker containers, production Compose projects, datasets, .env, or bind mounts have been modified.** Do not deploy this manifest.

## Starting point

- Eight staged projects at `/mnt/speedpool/appdata/stacks/staging/arr-compose-v2` (`arr-essential`, `arr-primary`, `arr-services`, `arr-utilities`, `autobrr`, `byparr`, `lidarr`, `whisparr`), each with `compose.yaml` and `.env`. Earlier staging had **8/8 `docker compose config -q` PASS** and Dockhand could read files; no deployment.
- Live projects: `arr-stack` (6), `arr-stack-secondary` (6), `arr-stack-stats` (3), `boxarr` (1), `byparr` (1), stopped `whisparr` (1). Actual user Compose definitions for the first three displayed `-` in `docker compose ls -a`; do not infer their paths.
- Shared production network `arr-stack_shared` had 15 attached containers in last inspection. `gluetun` carries qBittorrent network namespace and AirVPN/WireGuard; port 37410 was previously verified, not freshly tested. `jellystat-db` has isolated DB network.
- `praesidium` is DEGRADED; `speedpool` is single NVMe; automatic independent pool replication disabled. **Blocked for cutover** until storage is repaired and backup readiness verified.

## Live-to-staged service mapping

| Live Compose project | Services (current) | Staged owner | Data/risks to preserve |
|---|---|---|---|
| `arr-stack-secondary` | bazarr, cleanuparr, houndarr, unpackerr | `arr-utilities` | Preserve config and internal ARR connections. Cleanuparr's **observed** config: `/mnt/speedpool/appdata/stacks/cleanuparr/cleanuparr/config`; never trigger downloader deletion |
| `boxarr` | boxarr | `arr-utilities` | Identify actual mounts and owner before consolidating |
| `arr-stack` | flaresolverr, prowlarr, radarr, sonarr | `arr-primary` | Preserve ARR DB and API integrations; Unpackerr endpoints `http://sonarr:8989` and `http://radarr:7878` |
| `arr-stack-secondary` | jellyseerr | `arr-services` | Confirm application DB/config mount and login/integrations |
| `arr-stack-stats` | jellystat, jellystat-db, wizarr | `arr-services` | PostgreSQL consistency, volumes, credentials and network isolation; **do not copy live DB files for backup** |
| `arr-stack-secondary` | lidarr | standalone `lidarr` | Preserve data; decide later whether to retire (do not remove during migration) |
| `whisparr` | whisparr (intentionally stopped) | standalone `whisparr` | On-demand start, no accidental enable; shared-network change only at deliberate cutover |
| `byparr` | byparr | standalone `byparr` | Preserve existing working test setup; avoid double deployment |
| `arr-stack` | gluetun, qbittorrent | `arr-essential` | **Last**. Protect seed states, TorrentLeech, WireGuard/VPN-only egress, qBit config and forwarded port |
| no running container confirmed | autobrr | standalone `autobrr` | Staged only; separate enable/retire decision |
| planned only | qui | potential standalone `qui` | Not part of currently staged eight |

## Checks already completed (do not repeat by default)

- 8/8 staged Compose validation (previous session).
- Dockhand read access to staging `compose.yaml`/`.env` and its dual mount points.
- Actual Cleanuparr config path confirmed.
- Unpackerr repaired (Sonarr/Radarr URLs, `/media/downloads`, `delete_orig=false`).
- Active production project and container membership verified; no new staged projects deployed.
- Existing AMP ZIP backups and HomePi success heartbeat verified. See [Backup and Restore](Backup-and-Restore.md). These do **not** prove an independent restorable ARR backup.

## Unverified / manifest completion checklist

- [ ] Exact live `Mounts`: source, destination, type, and read-only status for each service; match against staged bind paths without exposing `.env` contents.
- [ ] Staged config *effective* paths and project labels; no unresolved host path, privilege or shared-network changes.
- [ ] Identify each PostgreSQL/SQLite DB and safe, application-consistent backup method; test restore for critical apps.
- [ ] Capture current container network attachments, hostname/service DNS names, ports and any static references; preserve `arr-stack_shared` where necessary.
- [ ] Inspect ownership/ACL mapping and original Compose ownership for rollback; never overwrite old data during dry run.
- [ ] Plan transition for projects split across new owners so old stack actions cannot inadvertently remove services already moved.
- [ ] Confirm downloads/media mount alignment and cross-dataset hardlink limitations, especially qBit/Sonarr/Radarr.
- [ ] Verify off-host backup of essential configs/dbs and that `praesidium` is ONLINE before cutover.
- [ ] Deploy one low-impact owner at a time, validate health and integrations, then document results. Gluetun/qBit last.

## Proposed cutover sequencing (conditional)

1. Backups, manifests, credentials in private vault, and rollback artifacts secured.
2. Review `arr-utilities` services individually with dependencies. Splitting live projects requires carefully scoped stop/recreate actions; **never** run `docker compose down` on a shared old owner just to migrate one service.
3. Move `arr-primary`, `arr-services` with DB backups and integration tests; avoid double-owning container names/networks.
4. Split Lidarr and address on-demand Whisparr/Byparr separately; preserve old Compose files for rollback.
5. Move `arr-essential` only after torrent state, VPN egress, ports and trackers can be tested and rollback verified.

## Next minimal diagnostic (not yet run)

Use a single read-only `docker inspect` pass over the affected containers, displaying just name/project/bind mount paths and destinations (never environment contents or credentials). Compare with staged YAML *without outputting interpolated secrets*. Commit a redacted source → target matrix when evidence is available.
