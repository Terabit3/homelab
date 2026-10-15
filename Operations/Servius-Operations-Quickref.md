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
- `parrot` Compose project, image, network and config removed; stopped `WinApps` and `whisparr` deliberately preserved. Parrot Uptime Kuma monitor #63 still pending deletion.
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
