# ARR ecosystem — Servius

> **Status as of Oct 16, 2026:** migration is **staged, not deployed**. For current decisions and safety notes, see [Servius assistant handoff](../../Assistant-Notes/Servius.md). For operational commands see [Servius quickref](../../Operations/Servius-Operations-Quickref.md).

## Currently deployed projects

| Compose project | Current active services |
|---|---|
| `arr-stack` | Gluetun, qBittorrent, Prowlarr, Radarr, Sonarr, FlareSolverr |
| `arr-stack-secondary` | Bazarr, Cleanuparr, Houndarr, Jellyseerr, Unpackerr, Lidarr |
| `arr-stack-stats` | Jellystat, PostgreSQL, Wizarr |
| Individual deployments | Whisparr, Boxarr, Byparr |
| Not currently running | Autobrr (standalone definition only) |

Live Compose configurations are under `/mnt/speedpool/appdata/stacks/<project>/`. Shared network `arr-stack_shared`. The historical Compose examples in [compose/](compose/) describe **old deployments**, not the new design; do not redeploy them over running services.

## Agreed future structure — NOT YET LIVE

| Future Dockhand project | Services |
|---|---|
| `arr-essential` | Gluetun, qBittorrent |
| `arr-primary` | FlareSolverr, Prowlarr, Radarr, Sonarr |
| `arr-services` | Jellyseerr, Jellystat, PostgreSQL, Wizarr |
| `arr-utilities` | Bazarr, Boxarr, Cleanuparr, Houndarr, Unpackerr |
| Standalone | Lidarr, Whisparr (on demand), Byparr (testing), Autobrr (evaluation); future Qui (experimental) |

Portable standard: matching Compose project and directory name; `compose.yaml`, `.env.example`, private `.env`; alphabetic services; relative persistent appdata mounts; host-specific media paths via environment variables. `standby/` stores inactive alternatives but does not itself stop Docker containers. Avoid duplicate Compose owners.

## Staging / cutover

Staged configuration at `/mnt/speedpool/appdata/stacks/staging/arr-compose-v2/`. The eight Compose configurations were validated 8/8 and Dockhand was able to read them; **no data moves or deployments have occurred**. Verify backups, compare actual mounts/credentials locally, and follow a controlled migration in reverse order of service criticality (VPN/qBit last). `praesidium` was DEGRADED as last checked; see [disk recovery runbook](../../Operations/TrueNAS-Disk-Replacement-and-Expansion.md).

Do not publish real credentials, full environment files, API keys or private tracker data. Do not assume a valid Compose file means the app will start or have correct data.
