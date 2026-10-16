# Services — where things run

**Purpose:** one fast service/ownership index. For a specific app, start at the [host-labelled application directory](Applications/README.md). For detailed October 14, 2026 observations, including the original full inventory and procedures, see [archived Services report](Archive/Services-2026-10-14.md). The archive is historical, **not current container state**.

## Hosts and managers

| Host | Purpose | How to operate |
|---|---|---|
| **Servius** | TrueNAS SCALE; storage, user Compose projects, TrueNAS Apps, AMP VM | TrueNAS UI for `ix-*` Apps; Dockhand for user-managed Compose; VM console/AMP for games |
| **HomePi** | DietPi Raspberry Pi; Pi-hole/Unbound, Tailscale, local Docker management and monitoring | Host-specific systemd and Docker; [HomePi reference](Infrastructure/homepi.md) |
| **h711nt** | Windows workstation; replicated files and selected archives | Windows/Syncthing; verify Receive Only folder settings |

## Servius TrueNAS-managed applications (October 14 evidence)
ARR Dashboard, Calibre-Web-Automated, Dockhand, FileBrowser Quantum, Immich, Jellyfin, Nginx Proxy Manager, Pi-hole, Syncthing, Tailscale and Uptime Kuma. Manage via TrueNAS-supported workflows, **not** a second Docker Compose owner.

## Servius user Compose applications
- **Current pre-migration ARR:** `arr-stack` (Gluetun, qBittorrent, Sonarr, Radarr, Prowlarr, FlareSolverr); `arr-stack-secondary` (Bazarr, Cleanuparr, Houndarr, Unpackerr, Jellyseerr, Lidarr); `arr-stack-stats` (Jellystat, PostgreSQL, Wizarr).
- Standalone or supporting: Authentik, Boxarr, Byparr, Dashy, Fail2ban, Homepage, Kuma container sync, Nextcloud, Whisparr and Windows/WinApps. Check intentionally stopped services before restarting.
- Autobrr was staged separately but had no running container at the Oct 16 migration audit.

**Future, NOT deployed:** `arr-essential`, `arr-primary`, `arr-services`, `arr-utilities`; standalone Lidarr, Whisparr, Byparr, Autobrr, experimental Qui and a standby area. See [ARR](Applications/ARR%20Stack/README.md) and [current handoff](Assistant-Notes/Servius.md).

## Find the right document

| Need | Reference |
|---|---|
| Application's host, role, manager and troubleshooting | [Applications](Applications/README.md) |
| Host hardware and storage | [Infrastructure](Infrastructure/README.md) |
| Current ARR cutover tasks and safety rules | [Servius handoff](Assistant-Notes/Servius.md) |
| Backups, recovery and maintenance | [Operations](Operations/README.md) |
| What to check during failure | [START-HERE](START-HERE.md) |
| October 14 full historical observations and original troubleshooting text | [Archived Services report](Archive/Services-2026-10-14.md) |
| Raw dated Docker capture | [Snapshots](Snapshots/README.md) |

## Read-only live check

Run on Servius, checking credentials before sharing outputs:

```bash
sudo zpool status
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Label "com.docker.compose.project"}}'
```

A dated document is not proof of present health. Never print or commit secrets, and don't redeploy duplicate Compose projects.
