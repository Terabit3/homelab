# Servius — Services, Operations & Troubleshooting

> **Inventory captured:** October 14, 2026, from `docker ps -a` supplied by the operator.  
> **Status:** Historical snapshot, **not live-verified**. Confirm current states, image tags and ports on Servius before using as a change plan.  
> **Scope:** TrueNAS SCALE host, Docker applications and important services outside Docker.  
> **Credentials:** Never store real `.env` contents, tokens, backup push URLs, SSH keys or passwords in this repository. Private copies are kept separately in `E:\Homelab-Private`.

## Start here if something breaks

1. Identify whether the problem is the **host/storage**, **Docker application**, **network/DNS/proxy**, or **AMP VM**.
2. Check TrueNAS alerts and pool health *before* restarting services if storage problems are suspected.
3. Look up the affected application below. Note which manager owns it: **TrueNAS Apps** (`ix-*`) or a separate Compose project.
4. Inspect the container and its last 100–200 log lines before making changes.
5. Confirm the relevant dataset, mount, network, DNS resolution and any dependent database/VPN are healthy.
6. **Back up data/config first** if a change might affect persistent files. Do not remove volumes or datasets as a troubleshooting shortcut.
7. Record the **symptom → cause → exact fix → verification** in that application's troubleshooting guide.

### First-response commands (run on Servius SSH)

```bash
sudo docker ps -a --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}'
sudo docker logs --tail 150 <container-name>
sudo docker inspect <container-name> --format '{{.State.Status}} {{if .State.Health}}{{.State.Health.Status}}{{end}}'
sudo docker inspect <container-name> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'
sudo zpool status
sudo zfs list -o name,used,avail,mountpoint
```

**Do not copy a literal `<container-name>` into the terminal.** Replace it with an actual name listed below. Prefer inspecting logs and storage before restarting. Docker inspect *without a restrictive format* may reveal passwords and should not be copied into GitHub or chat unredacted.

## System at a glance

| Item | Snapshot / reference |
|---|---|
| Host | `Servius`, TrueNAS SCALE (exact currently installed version **requires live verification**) |
| Hardware | Intel Xeon E-2246G, Supermicro X11SCA-F, 64 GB ECC; full details in `Infrastructure/` |
| Pools | `speedpool` (NVMe app data and VM data); `praesidium` (HDD storage and backups) |
| Apps and Compose | 49 Docker containers: **41 running, 8 exited** at capture time |
| Container dashboard | Dockhand is deployed as a TrueNAS-managed app; distinguish management UI from actual Compose ownership |
| AMP games | AMP lives in a **Debian VM**, **not** the TrueNAS host's Docker inventory |
| Key paths | `/mnt/speedpool/appdata/`, `/mnt/speedpool/appdata/stacks/`, `/mnt/praesidium/backups/amp/` |
| Private configuration archive | `E:\Homelab-Private\Servius\` on h711nt; only some applications have verified complete backups |

**Storage warning:** `praesidium` was previously reported degraded during a power-cabling issue; do not assume it is resolved. Check `zpool status` and current TrueNAS alerts before risky maintenance.

## Docker inventory — October 14 capture

**Interpretation:** “healthy” means Docker reported healthy at the snapshot date, not that an application is healthy now. Stopped initialization, upgrade and permission containers may be expected.

### TrueNAS Apps (TrueNAS owns deployment)

| Application / Compose project | Primary containers / image at capture | Snapshot status |
|---|---|---|
| ARR Dashboard — `ix-arr-dashboard` | `ix-arr-dashboard-arr-dashboard-1` — `ghcr.io/kha-kis/arr-dashboard:2.24.1` | Running, healthy |
| Calibre-Web Automated — `ix-calibre-web-automated` | `ix-calibre-web-automated-calibre-web-automated-1` — `ghcr.io/crocodilestick/calibre-web-automated:v4.0.8` | Running, healthy |
| Dockhand — `ix-dockhand` | `ix-dockhand-dockhand-1` — `fnsys/dockhand:v1.0.53` | Running, healthy |
| FileBrowser Quantum — `ix-filebrowser-quantum` | `ix-filebrowser-quantum-filebrowser-quantum-1` — `ghcr.io/gtsteffaniak/filebrowser:2.0.0-stable` | Running, healthy; init/permissions helpers exited 0 |
| Immich — `ix-immich` | server, machine-learning, PostgreSQL/VectorChord, Valkey | Running, healthy; upgrade/permissions helpers exited 0 |
| Jellyfin — `ix-jellyfin` | `ix-jellyfin-jellyfin-1` — `ghcr.io/jellyfin/jellyfin:12.2` | Running, healthy; permissions helper exited 0 |
| Nginx Proxy Manager — `ix-nginx-proxy-manager` | `ix-nginx-proxy-manager-npm-1` — `jc21/nginx-proxy-manager:2.16.0` | Running, healthy |
| Pi-hole — `ix-pihole` | `ix-pihole-pihole-1` — `pihole/pihole:2026.09.0` | Running, healthy |
| Syncthing — `ix-syncthing` | `ix-syncthing-syncthing-1` — `syncthing/syncthing:2.1.6` | Running, healthy |
| Tailscale — `ix-tailscale` | `ix-tailscale-tailscale-1` — `ghcr.io/tailscale/tailscale:v1.102.5` | Running, healthy |
| Uptime Kuma — `ix-uptime-kuma` | `ix-uptime-kuma-uptime-kuma-1` — `ghcr.io/louislam/uptime-kuma:2.5.6` | Running, healthy |

**TrueNAS Apps caution:** Manage these through TrueNAS's supported Apps interface. Don't assume a Compose file generated by TrueNAS is safe to edit directly or redeploy in Dockhand.

### User Compose projects and supporting containers

| Compose project | Container(s) | Snapshot status / relationship |
|---|---|---|
| `arr-stack` | `flaresolverr`, `gluetun`, `prowlarr`, `qbittorrent`, `radarr`, `sonarr` | All running; Gluetun healthy; qBittorrent's connectivity may depend on Gluetun |
| `arr-stack-secondary` | `bazarr`, `cleanuparr`, `houndarr`, `jellyseerr`, `unpackerr` | All running; Houndarr healthy |
| `arr-stack-stats` | `jellystat`, `jellystat-db`, `wizarr` | All running; Jellystat/Wizarr healthy |
| `authentik` | `authentik-server-1`, `authentik-worker-1`, `authentik-postgresql-1` | All running, healthy |
| `boxarr` | `boxarr` | Running, healthy |
| `byparr` | `byparr` | Running, healthy; **separate project**, not `arr-stack-secondary` |
| `dashy` | `Dashy` | Running, healthy |
| `fail2ban` | `fail2ban` | Running, healthy |
| `homepage` | `homepage` | Running, healthy; live configuration paths below |
| `kuma-sync` | `kuma-container-sync` | **Running, unhealthy** — investigate rather than assume all monitoring discovery works |
| `nextcloud` | `nextcloud`, `nextcloud-cron`, `nextcloud-db`, `nextcloud-redis` | All running; DB healthy |
| `windows` | `WinApps` | Exited (0); previously used Dockur Windows; may be intentionally disabled |
| `parrot` | `parrot` | Exited (137); cause **unverified** |
| `whisparr` | `whisparr` | Exited (0); purpose/status to confirm |

**Correction from legacy `Services.md`:** Bazarr, Cleanuparr and Unpackerr belong to `arr-stack-secondary`, Wizarr belongs to `arr-stack-stats`, and Byparr is its own Compose project. The above reflects the actual container labels in the supplied October 14 inventory.

### Application-specific configuration already documented

| Application | Current documentation | Live host path | Private recovery location |
|---|---|---|---|
| ARR stacks | `Applications/ARR Stack/README.md` plus `compose/` | `/mnt/speedpool/appdata/stacks/<stack>/` | `E:\Homelab-Private\Servius\<stack>\` (three stacks verified) |
| Homepage | `Applications/Homepage/README.md`, Compose, template, and `config/` | `/mnt/speedpool/appdata/homepage/compose.yaml`, `homepage.env`, `config/` | `E:\Homelab-Private\Servius\homepage\` (Compose, env and nine config files hash-verified) |
| Windows (Dockur) | `Applications/Windows/README.md` | To verify | Private `windows.env` backup **not yet verified** |

Other application config paths, mounts and backup coverage must be confirmed individually. A live container does **not** imply its data is backed up.

## TrueNAS emergency and disk procedures

For physical disk issues, pool errors, storage expansion, boot failures or recovering deleted data, use the dedicated runbooks. They distinguish read-only diagnosis from destructive actions and are written for Servius’s `praesidium` RAIDZ2 pool and single-NVMe `speedpool`:

- [TrueNAS Troubleshooting & Recovery Runbook](Operations/TrueNAS-Troubleshooting-Runbook.md)
- [TrueNAS Disk Replacement & Expansion](Operations/TrueNAS-Disk-Replacement-and-Expansion.md)

**Version caution:** Procedures reference TrueNAS SCALE 24.10 documentation. Verify the *currently installed release*, current pool topology, backups, and exact disk serials before making changes. These guides are reference instructions, not evidence that an error is happening now.

## Storage and backup operations

### AMP game backups — previously verified workflow

| Component | Details |
|---|---|
| Source | AMP Debian VM; per-instance ZIP backups managed by AMP |
| VM sync script | `/usr/local/sbin/amp-backup-sync.sh` |
| VM notification wrapper | `/usr/local/sbin/amp-backup-notify.sh` |
| VM schedule | `/etc/cron.d/amp-backup-sync` at **06:00**, configured with Mountain time |
| Destination on Servius | `/mnt/praesidium/backups/amp/<instance>/` |
| TrueNAS retention script | `/mnt/speedpool/appdata/scripts/amp-backup-retention.sh` |
| Cleanup schedule | TrueNAS task at **07:00**; policy targets about 14 days and preserves the newest archive per instance |
| Syncthing Servius folder | `amp-game-backups`, Send Only; source mount read-only at `/backups/amp` |
| Syncthing h711nt folder | `E:\TrueNAS Syncthing\backups\amp`, Receive Only; staggered versioning up to 30 days |
| Monitoring | Discord `#backups`; Uptime Kuma `AMP Daily Backups` Push monitor (URLs are private) |

**Verification recorded on Oct 14:** Valheim and Project Zomboid archives matched SHA-256 between Servius and h711nt. **A full game save restore has not yet been proven.** The retention task was configured but its first actual aged deletion was not verified at the time of these notes.

**Important limitation:** The VM sync script discovers instances with recent ZIP archives; a failed AMP per-instance ZIP job might silently leave an instance out. Consider separate freshness checks for critical game saves.

### Safety and maintenance reminders

- Inspect `zpool status`, disk alerts and actual snapshot/backup health before destructive operations.
- A successful Syncthing transfer is **not** a substitute for an independent backup; verify restore usability.
- Preserve native application databases/settings, not only a container's Compose YAML.
- TrueNAS configuration exports, encrypted offsite copies, and full recovery tests are separate from this text documentation.
- Some system tasks and snapshots have historically failed; verify current task history rather than assuming a schedule proves success.

## Troubleshooting by symptom

| Symptom | First checks | Documentation/action |
|---|---|---|
| ARR requests/downloads fail | `gluetun` health/logs, qBittorrent networking, Sonarr/Radarr/Prowlarr logs and credentials | `Applications/ARR Stack/` |
| Homepage shows a red/down service | Internal `siteMonitor` address, container DNS/network, NPM health, widgets' environment references | `Applications/Homepage/README.md` |
| Reverse proxy/auth issues | NPM container, host DNS, Authentik availability, certificate state | NPM and Authentik app docs (expand when verified) |
| Syncthing not syncing | Send Only / Receive Only modes, ownership, destination free space, out-of-sync paths | Check both devices; avoid changing direction blindly |
| AMP backup notification fails | AMP ZIP creation, VM sync script/log, Servius destination availability, Discord/Kuma reachability | AMP procedure above |
| ZFS I/O or pool alerts | `zpool status`, `zpool events`, TrueNAS alerts, cabling and SMART history | `Troubleshooting/` and `Infrastructure/` |
| `kuma-container-sync` unhealthy | `sudo docker logs --tail 150 kuma-container-sync` and health check output | **Open issue**; do not assume this is fixed |
| Unexpected stopped container | Was it intentionally stopped? `sudo docker inspect ...` and previous logs | Don't restart or remove purely because status says Exited |

## Standard operating procedure

### Add or change an application

1. Identify the **owning deployment manager** (TrueNAS Apps or user Compose stack). Avoid dual management.
2. Record the image tag, config location, persistent mounts, network and dependencies.
3. Verify a usable application-data/config backup when the planned change could damage state.
4. Deploy or update through that owner; check logs, functionality, proxy/auth, health and monitoring.
5. Update the app README and refresh this inventory. Record what was learned or repaired.

### Retire an application

1. Confirm it is truly unused; identify integrations, reverse-proxy hosts, monitors, data mounts and scheduled tasks.
2. Save any desired configuration and data outside Git history.
3. Stop/remove only through the correct deployment manager. Retain data until no longer needed.
4. Mark the app **retired** and keep reusable troubleshooting knowledge.

### Template for a solved problem

```markdown
# Symptom: [what broke]

- Date / platform version:
- Affected app and container:
- Error message (redacted):
- Cause (confirmed vs suspected):
- Working fix: exact steps/commands
- How I verified it:
- What did NOT work:
- What to remember next time:
```

## Refresh the snapshot safely

Run on **Servius SSH**; output excludes explicit environment variables and secrets but may contain internal hostnames and published ports:

```bash
sudo docker ps -a --format '{{.Names}}|{{.Image}}|{{.Status}}|{{.Label "com.docker.compose.project"}}'
sudo zpool status
```

Compare project membership with this page and the automatically captured `Snapshots/Docker-Inventory.md`. Do not paste raw `docker inspect`, `.env`, or TrueNAS credential output into GitHub.

### Checks still pending / not established as current

- Current TrueNAS SCALE version and latest health alerts.
- Current `praesidium` status following the previously reported power-cabling issue.
- Whether `kuma-container-sync` remains unhealthy and its root cause.
- Actual manager ownership and restore instructions for non-TrueNAS Compose projects.
- First verified cleanup by AMP archive retention; full Valheim and Zomboid restore tests.
- State of the larger `speedpool-appdata` Syncthing synchronization.
- TrueNAS backup-task last-success timestamps and independent offsite recovery capability.
- Actual published host ports, routes and firewall exposure: **not revalidated by this document**.

---

*This document is designed for troubleshooting and recovery. Reverify volatile values when updating Servius. Never silently replace verified history with guesses.*
