# Servius operations quick reference

**Snapshot date:** October 14–15, 2026. Confirm live status before making changes.

## Paths and ownership
- `/mnt/speedpool/appdata/`: persistent app data on **single, nonredundant NVMe**.
- `/mnt/speedpool/appdata/stacks/`: user Compose projects, including `arr-stack`, `arr-stack-secondary`, `arr-stack-stats`.
- `/mnt/speedpool/appdata/homepage/`: `compose.yaml`, secret `homepage.env`, and `config/`.
- `/mnt/praesidium/backups/amp/<instance>/`: AMP game archives.
- `/mnt/praesidium/backups/homepi/homepi-backup/`: HomePi daily archives.
- AMP **Debian VM**, not TrueNAS host: `/usr/local/sbin/amp-backup-sync.sh`, `/usr/local/sbin/amp-backup-notify.sh`, `/etc/cron.d/amp-backup-sync`.
- Full reference: [Important Paths](Important-Paths.md).

## Applications
- Dockhand `ix-dockhand-dockhand-1` is a TrueNAS-managed app. Gradually migrate only appropriate user Compose projects from Portainer/Dockge; avoid multiple owners and preserve `homepage.env` editing.
- Servius Kuma `ix-uptime-kuma-uptime-kuma-1`, version 2.5.6 observed Oct 15, port 31050. Planned HomePi backup monitoring via existing **WebUI**, not another monitor on HomePi.
- NPM `ix-nginx-proxy-manager-npm-1` and Authentik cover network/app access; verify routes and policies live. `kuma-container-sync` was unhealthy in Oct 14 capture.
- AMP hosts game instances in Debian VM; its ZIP sync to Servius and h711nt was previously hash-verified but restore untested.

## October 15 verified operational status (not live)
- Docker cleanup reduced reported image storage from **308.2 GB to 64.03 GB**; 113 images remained and ~41.17 GB was still classified reclaimable. 340 manifest-selected unused images removed with zero failures; preserve container images, rollback candidates and **all Docker volumes**. Do not run blanket image/volume pruning.
- `parrot` Compose project, image, network and config removed; stopped `WinApps` and `whisparr` deliberately preserved. Parrot Uptime Kuma monitor #63 subsequently removed and verified.
- `ix-dockhand-dockhand-1`, `ix-calibre-web-automated-calibre-web-automated-1`, and `kuma-container-sync` were healthy after cleanup. Sync every 300s; 74/74 active monitors had notification coverage when checked.
- Broken cron ID 1 disabled. Native periodic snapshot tasks remained enabled, but snapshot age/allow-empty should be checked individually.
- `praesidium` **DEGRADED** with one unavailable RAIDZ2 member, attributed by operator to bad SATA power path; cable ordered, repair unverified. Do not assume hardware repaired.
- Both MEGA cloud sync tasks disabled (one previous FAILED), and speedpool replication disabled. Check independent recovery options.
- TrueNAS user home `/mnt/speedpool/homes/timhu`, blue Bash prompt persisted through SSH login. `timhu` Match User SSH password override still allows passwords; key-only remains pending.
- Refer to [October 15 changelog](../CHANGELOG.md) for exact screenshots-derived evidence, rollback and outstanding work.

## Read-only first response
```bash
sudo zpool status
sudo zfs list -o name,used,avail,mountpoint
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
sudo docker logs --tail 100 ix-uptime-kuma-uptime-kuma-1
sudo docker inspect ix-dockhand-dockhand-1 --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'
```

**Caution:** Manage TrueNAS `ix-*` Apps through supported TrueNAS mechanisms. A historical `praesidium` degraded/power-cable issue and failed cloud-sync backup need current checks. Never commit full `docker inspect` env or private config values.

## Change sequence
Check pool/alerts → locate owner and volume → backup/snapshot → one change → verify service and monitoring → update changelog/runbook/roadmap.

## October 16 ARR, Dockhand, and VPN findings
- Dockhand: added a matching host-path bind mount at /mnt/speedpool/appdata/stacks alongside /appdata/stacks, resolving Whisparr's environment-file save failure. Keep both mappings.
- Homepage, Dashy, Fail2ban: targeted group changes to GID 568 (apps) enabled Dockhand Compose file read/write. Underlying ZFS has NFSv4 ACLs; POSIX setfacl was unsupported.
- Compose validation passed for arr-stack, arr-stack-secondary, arr-stack-stats, boxarr, byparr, whisparr. Shared network arr-stack_shared carries most ARR services. qBittorrent shares Gluetun's namespace. Jellystat database remains on isolated internal network.
- Gluetun-to-service Docker DNS/HTTP passed for Radarr, Sonarr, Prowlarr, Bazarr, Cleanuparr and FlareSolverr. Jellyseerr redirected to /login then HTTP 200.
- AirVPN Gluetun/WireGuard and qBittorrent listening port 37410 verified; external AirVPN TCP port test was open and TCP+UDP configured afterward. Preserve existing torrent VPN gateway.
- Cleanuparr: Download Cleaner and Unlinked Downloads recommended disabled due TorrentLeech seeding risks. Confirm saved state. Cross-dataset hardlinks are not possible in current separate ZFS child dataset layout; preserve torrents labeled cleanuparr-unlinked.
- Cleanuparr logs: stale Readarr and intermittently stopped Whisparr health checks. Bazarr logs: SUBDL external connection errors, some DNS and HTTP errors; not an ARR bridge failure.
- Unpackerr: starts with 0 Sonarr and 0 Radarr servers; URLs/API keys commented in config, folder scanning interval 0s. Both API keys available locally. Automated repair attempt aborted before any changes due to erroneous exact section-count check; follow-up audit found exactly one [[sonarr]] at line 116 and [[radarr]] at line 137. Repair pending; protect seeded archives.
- HomePi already advertises a Tailscale residential exit and subnet routes with forwarding/NAT enabled. Existing Servius Tailscale advertises overlapping routes but IPv6 forwarding is disabled, causing a warning. A distinct AirVPN exit-node identity is planned, not deployed. Do not alter TrueNAS host default route.
- Kasm, disposable browser/desktop, VPN monitoring and routed VPN-egress bridge are future ideas, not deployed.
