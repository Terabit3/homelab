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

## Verified live mounts (Oct 17, 2026)

Read-only `docker inspect` output provided by operator; all mount entries reported `RW=True`. Paths are **live**, NOT proposed new destination paths. No environment values examined.

| Staged owner | Container | Live source | Container destination | Type |
|---|---|---|---|---|
| `arr-essential` | `qbittorrent` | `/mnt/speedpool/appdata/stacks/arr-stack/qbit` | `/config` | `bind` |
| `arr-essential` | `qbittorrent` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-essential` | `gluetun` | `/mnt/speedpool/appdata/stacks/arr-stack/gluetun` | `/config and /gluetun` | `bind twice` |
| `arr-primary` | `prowlarr` | `/mnt/speedpool/appdata/stacks/arr-stack/prowlarr` | `/config` | `bind` |
| `arr-primary` | `prowlarr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-primary` | `sonarr` | `/mnt/speedpool/appdata/stacks/arr-stack/sonarr` | `/config` | `bind` |
| `arr-primary` | `sonarr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-primary` | `radarr` | `/mnt/speedpool/appdata/stacks/arr-stack/radarr` | `/config` | `bind` |
| `arr-primary` | `radarr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-primary` | `flaresolverr` | `/mnt/.ix-apps/docker/volumes/56b27f755fb9713c057f4a6dc55aca07c74ad35bef0cc2a71568e3d31a2d28ba/_data` | `/config` | `Docker volume` |
| `arr-utilities` | `bazarr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/bazarr/config` | `/config` | `bind` |
| `arr-utilities` | `bazarr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-utilities` | `cleanuparr` | `/mnt/speedpool/appdata/stacks/cleanuparr/cleanuparr/config` | `/config` | `bind` |
| `arr-utilities` | `cleanuparr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-utilities` | `houndarr` | `/mnt/.ix-apps/docker/volumes/29f25e6edd8064c143714a2017763d732fa17d3de1fe3b67fdb292297d3d8bb7/_data` | `/data` | `Docker volume` |
| `arr-utilities` | `houndarr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/houndarr` | `/config` | `bind` |
| `arr-utilities` | `unpackerr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/unpackerr/config` | `/config` | `bind` |
| `arr-utilities` | `unpackerr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |
| `arr-utilities` | `boxarr` | `/mnt/speedpool/appdata/stacks/boxarr/config` | `/config` | `bind` |
| `arr-services` | `jellyseerr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/jellyseerr` | `/app/config` | `bind` |
| `arr-services` | `jellystat` | `/mnt/speedpool/appdata/stacks/arr-stack-stats/jellystat/backup-data` | `/app/backend/backup-data` | `bind` |
| `arr-services` | `jellystat-db` | `/mnt/speedpool/appdata/stacks/arr-stack-stats/jellystat-db/data` | `/var/lib/postgresql/data` | `bind` |
| `arr-services` | `wizarr` | `/mnt/speedpool/appdata/stacks/arr-stack-stats/wizarr/data` | `/data` | `bind` |
| `lidarr` | `lidarr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/lidarr/config` | `/config` | `bind` |
| `lidarr` | `lidarr` | `/mnt/speedpool/appdata/stacks/arr-stack-secondary/lidarr/music` | `/music` | `bind` |
| `lidarr` | `lidarr` | `/mnt/praesidium/appdata/arr_common` | `/media` | `bind` |

**Additional findings:**
- `byparr` has **no declared mounts** in the observed `docker inspect`.
- `flaresolverr` has a Docker-managed volume at `/config`; `houndarr` has a Docker-managed volume at `/data` **plus** a bind at `/config`. Do not assume these are disposable or migrate them as if already under staged per-stack directories.
- `jellystat-db` stores PostgreSQL on a bind mount. Use an application-consistent backup and controlled cutover rather than raw copying an active database.
- `gluetun` mounts the **same host directory** to both `/config` and `/gluetun`; maintain semantics and check permissions.
- Shared `/media` mount is `/mnt/praesidium/appdata/arr_common` for qBittorrent, Prowlarr, Sonarr, Radarr, Bazarr, Cleanuparr, Unpackerr and Lidarr. Preserve this shared path mapping to avoid import failures.
- Lidarr has an **additional** `/music` bind under the old `arr-stack-secondary` directory, in addition to `/media`.
- All new staged relative mount paths, ownership, network memberships, static labels, container ports and startup dependencies **remain unverified**. Do not assume staged parity from these live mounts.

## October 17 staged-media and special-volume checks

- Staged `MEDIA_PATH` equals live `/mnt/praesidium/appdata/arr_common` in all five relevant projects: `arr-essential`, `arr-primary`, `arr-utilities`, `lidarr`, and `whisparr`. **MATCH x5**; the secret environment file was not published.
- Live FlareSolverr Docker volume `/config`: `du -sh` reported `512` (effectively empty). Staged Compose has no matching mount. Do not claim disposable until behavior/config use is checked.
- Live Houndarr Docker volume `/data`: `du -sh` reported `767K`; staged Compose has no `/data` mount. Treat as potentially persistent until file types and application behavior are verified.
- Other staged mappings in the operator's short-form YAML inventory follow relative-per-stack directories, which **are not the current live paths**. A controlled migration/copy is required; no data was moved.
- Neither volume was modified. No staged Compose or deployment changes occurred.

## Unverified / manifest completion checklist

- [x] Capture exact live `Mounts` for 17 running ARR-related containers (Oct 17); see table below. All mounts reported RW=True. **Staged short-form mappings inspected; effective target base and long-form checks remain outstanding.**
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

Compare the eight staged YAML bind mount expressions with these verified live mounts **without interpolating or printing `.env` values**. Include Docker-managed volume migration decisions, DB-consistent backup requirements, and project cutover controls. Do not alter production containers or copy data yet.
