# Servius — operations quick reference

**Host:** Servius (TrueNAS SCALE). **Dated context:** Oct 16, 2026; verify all volatile values live. This is the short command page, **not** the history log or migration plan.

## First response (read-only)

```bash
sudo zpool status -v
sudo zpool list
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Label "com.docker.compose.project"}}'
sudo systemctl --failed --no-pager
```

If storage has errors, diagnose pools and hardware **before** restarting services. For full escalation steps see [START-HERE](../START-HERE.md).

## Management boundaries

| Owner | How to manage | Notes |
|---|---|---|
| TrueNAS Apps | TrueNAS Apps interface | Containers typically have `ix-*` prefixes; do not clone into Dockhand |
| User-managed Compose | Dockhand / corresponding original project | Stacks under `/mnt/speedpool/appdata/stacks/`; preserve single ownership |
| AMP game servers | Debian VM / AMP interface | AMP VM scripts do not execute on TrueNAS |
| HomePi services | HomePi SSH / its Docker manager | Separate host, separate config, separate outages |

## Everyday inspections

```bash
# Only actual names from docker ps:
sudo docker logs --tail 100 gluetun
sudo docker logs --tail 100 qbittorrent
sudo docker inspect gluetun --format '{{.State.Status}} {{if .State.Health}}{{.State.Health.Status}}{{end}}'
sudo docker inspect ix-dockhand-dockhand-1 --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'
sudo zfs list -o name,used,avail,mountpoint
```

These targeted queries avoid printing environment credentials. Never publish unfiltered `docker inspect`.

## High-impact cautions

- **Storage:** `praesidium` last reported DEGRADED with one missing RAIDZ2 member. `speedpool` is a single nonredundant NVMe. Confirm live pool state and independent backups before migration, updates or deletions.
- **Torrent/VPN:** qBittorrent shares Gluetun's network namespace; preserve AirVPN port `37410`, seeded TorrentLeech torrents, shared ARR network and database mounts. Never restart both without planning.
- **ARR migration:** Eight Compose projects validated in `/mnt/speedpool/appdata/stacks/staging/arr-compose-v2/`; **none deployed** by the Oct 16 audit. The current active project names remain `arr-stack`, `arr-stack-secondary`, `arr-stack-stats`. See [current Servius handoff](../Assistant-Notes/Servius.md).
- **Dockhand:** Container `ix-dockhand-dockhand-1`, observed UID/GID 568:568. Staged Compose and `.env` were readable **inside the container**; previous host `runuser -u '#568'` check was invalid.
- **Unpackerr:** Oct 16 observed configured Sonarr and Radarr, `delete_orig=false`; verify seeded archive preservation.
- **Backups:** Local ZFS snapshots and Syncthing live appdata copies are not independently sufficient for database recovery. Previous MEGA sync and speedpool replication were disabled.

## Find detail once — avoid duplicating it here

| Need | Canonical file |
|---|---|
| Detailed path maps and owners | [Important paths](Important-Paths.md) |
| Backup scripts, validity and restore tests | [Backup and restore](Backup-and-Restore.md) |
| Actual migration plan and verified decisions | [Servius handoff](../Assistant-Notes/Servius.md) |
| All applications and managers | [Services](../Services.md) |
| October 14–15 detailed operational changes | [Historical recap](2026-10-14-15-Recap.md) and [changelog](../CHANGELOG.md) |
| Full TrueNAS diagnostics | [Troubleshooting runbook](TrueNAS-Troubleshooting-Runbook.md) |

Change process: identify owner → confirm data and backup → one authorized change → verify health, persistence, DNS and monitoring → record result.
