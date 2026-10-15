# Important directories and paths

**Reference captured:** 2026-10-14–15. These are paths for inspection and recovery, **not** permission to modify/delete them. Confirm mounts, owners and application versions live before any write. Do not publish contents of secret-bearing files.

## Servius — TrueNAS SCALE host

| Path | Purpose | Confidence |
|---|---|---|
| `/mnt/speedpool/appdata/` | Fast persistent application data on nonredundant NVMe | Recorded |
| `/mnt/speedpool/appdata/stacks/` | User Compose project roots (separate from TrueNAS ix apps) | Verified |
| `/mnt/speedpool/appdata/stacks/arr-stack/compose.yaml` | Core ARR deployment | Verified Oct 15 |
| `/mnt/speedpool/appdata/stacks/arr-stack-secondary/compose.yaml` | Bazarr, Cleanuparr, Seerr etc. | Verified Oct 15 |
| `/mnt/speedpool/appdata/stacks/arr-stack-stats/compose.yaml` | Jellystat/Wizarr group | Verified Oct 15 |
| `/mnt/speedpool/appdata/homepage/compose.yaml` | Homepage Compose | Previously verified |
| `/mnt/speedpool/appdata/homepage/homepage.env` | Private Homepage variables; **never commit** | Previously verified |
| `/mnt/speedpool/appdata/homepage/config/` | Homepage YAML config | Previously verified |
| `/mnt/speedpool/appdata/scripts/amp-backup-retention.sh` | AMP archive retention script | Previously documented |
| `/mnt/praesidium/backups/amp/<instance>/` | AMP archives by instance | Previously verified |
| `/mnt/praesidium/backups/homepi/homepi-backup/` | HomePi `homepi-apps-*.tar.gz` | Verified Oct 15 |
| `/mnt/praesidium/` | Bulk HDD pool/datasets | Recorded |

**Storage:** `speedpool` has no disk redundancy; `praesidium` historical 4×12 TB RAIDZ2 had a cabling/degraded report. Check `sudo zpool status` first. TrueNAS Apps-managed `ix-*` configuration is generated/managed by TrueNAS, **not automatically owned by Dockhand**.

### Selected container names (historical October 14–15)

- Servius Uptime Kuma: `ix-uptime-kuma-uptime-kuma-1` (v2.5.6 observed October 15; host port 31050).
- Dockhand: `ix-dockhand-dockhand-1`; Nginx Proxy Manager: `ix-nginx-proxy-manager-npm-1`.
- HomePi and Servius both have had Pi-hole-related services. Verify DNS role before changing upstream/resolvers.
- `kuma-container-sync` historically unhealthy; investigate, don't assume recovered.

## HomePi — DietPi, Raspberry Pi 3B+

| Path | Purpose | Confidence |
|---|---|---|
| `/opt/homepi-backup/daily.sh` | Root-executed verified backup workflow | Verified Oct 15 |
| `/opt/homepi-backup/daily.sh.bak` | Original pre-fix script (contains old FTL history snapshot step) | Verified Oct 15 |
| `/opt/homepi-backup/retention.py` | Python retention logic, default dry-run and `--apply` | Verified Oct 15 |
| `/home/dietpi/homepi-first-backup.sh` | Existing private SSH connection configuration, sensitive values; **do not publish** | Verified Oct 15 |
| `/etc/systemd/system/homepi-backup.service` | Daily backup systemd service, `TimeoutStartSec=300` | Verified Oct 15 |
| `/etc/systemd/system/homepi-backup.timer` | ~03:30 daily timer, persistent, randomized delay | Verified Oct 15 |
| `/run/lock/homepi-backup.lock` | Prevent concurrent backup runs (`flock`) | Script observed |
| `/var/tmp/homepi-backup.*` | Temporary backup staging, removed by EXIT trap on normal cleanup | Script observed |
| `/etc/pihole/` | Pi-hole configuration; `gravity.db` snapshot included | Verified Oct 15 |
| `/etc/pihole/pihole-FTL.db` | Hot query history DB, **excluded** from automatic archive | Verified Oct 15 |
| `/etc/unbound/` | Unbound configuration | Script observed |
| `/var/lib/tailscale/` | Tailscale state | Script observed |
| `/opt/dockhand/` | Dockhand configuration and SQLite | Script observed |
| `/opt/uptime-kuma/` | HomePi-local Kuma config path copied by current script; actual service status requires checking | Script observed |
| `/home/dietpi/docker/uptimek/` | Kuma database `kuma.db`, support files and screenshots | Archive contents verified |
| `/etc/docker/` | Host Docker daemon configuration where present | Script observed |

**HomePi limitations:** FTL query history cannot be restored from current archives. HomePi Kuma config may be legacy; the chosen **central monitoring destination is Servius**, not a new HomePi Kuma. Backups exclude some live `*.db*` copies then snapshot selected databases individually.

## AMP Debian VM (on Servius)

| Path | Purpose | Confidence |
|---|---|---|
| `/usr/local/sbin/amp-backup-sync.sh` | Transfer per-instance ZIPs to Servius | Previously verified |
| `/usr/local/sbin/amp-backup-notify.sh` | Backup notification wrapper | Previously verified |
| `/etc/cron.d/amp-backup-sync` | ~06:00 scheduled sync | Previously verified |
| Servius `/mnt/praesidium/backups/amp/<instance>/` | Destination of AMP backups | Previously verified |

Do not treat AMP VM paths as paths on the TrueNAS host.

## Workstation h711nt

| Path | Purpose |
|---|---|
| `E:\\Homelab-Private\\Servius\\` | Private copies of configurations, including sensitive files; excluded from Git |
| `E:\\TrueNAS Syncthing\\backups\\amp\\` | AMP backup Receive Only replica |
| `E:\\Users\\timhu\\Documents\\GitHub\\homelab\\` | Working checkout reported by operator; confirm before scripts |

## Read-only discovery commands

```bash
# Servius:
sudo zpool status
sudo zfs list -o name,used,avail,mountpoint
sudo docker ps -a --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
sudo docker inspect <actual-container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'

# HomePi:
systemctl status homepi-backup.timer --no-pager
systemctl show homepi-backup.service -p Result -p ExecMainStatus
sudo journalctl -u homepi-backup.service -n 30 --no-pager
```

Never execute example angle-bracket placeholders literally. Before manipulating `/mnt` content, confirm pool/device identity and backups.
