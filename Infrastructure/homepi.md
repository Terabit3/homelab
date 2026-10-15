# homepi — System Reference

> **Status:** Imported from legacy documentation; verify current services and addresses on the Raspberry Pi. Do not assume the old application list is current.

## Purpose and hardware

- Raspberry Pi 3 Model B+ (legacy notes call this “3+”); quad-core ARM Cortex-A53; 1 GB RAM.
- DietPi, a minimal Debian-based OS (verify installed version).
- Headless, low-power home-network services.

## Previously documented services

| Service | Purpose | Verification needed |
|---|---|---|
| Pi-hole | DNS filtering | Confirm active DNS role and admin URL |
| Unbound | Local recursive DNS resolver | **Unbound is not automatically DNS-over-HTTPS**; check actual configuration |
| Tailscale | Remote access | Confirm routes, ACLs and address |
| Portainer | Container management | Confirm still installed and used |
| Uptime Kuma | Monitoring | Confirm whether it still runs here or on Servius |

## Historical connection information

Legacy notes used `ssh pi@192.168.50.26`. Confirm the current SSH account and IP rather than treating this as a guaranteed command.

## Useful checks (run on homepi)

```bash
hostnamectl
ip -brief address
systemctl --failed
```

For DNS checks, document the actual Pi-hole upstream and whether Unbound is recursive, forwarding, or configured for encrypted DNS. Avoid changing DNS servers until the role of both homepi and Servius is clear.


## October 15, 2026 verified backup update

- DietPi 10.7.2 on Raspberry Pi 3B+; LAN IP observed `192.168.50.26` (confirm before connecting).
- HomePi `/opt/homepi-backup/daily.sh` performs nightly backup through enabled `homepi-backup.timer` and `homepi-backup.service` (~03:30 Mountain, randomized delay up to five minutes, persistent catch-up, 300-second whole-job timeout).
- Destination: Servius `/mnt/praesidium/backups/homepi/homepi-backup/`; private SSH configuration is read from `/home/dietpi/homepi-first-backup.sh`. Do not publish its contents.
- Source config includes `/etc/pihole/`, `/etc/unbound/`, `/var/lib/tailscale/`, `/opt/dockhand/`, Uptime Kuma config directories and Docker config when present. Selected SQLite databases use consistent online snapshots.
- `/etc/pihole/pihole-FTL.db` query history is **excluded** after its SQLite backup loop repeatedly copied many gigabytes; Pi-hole `gravity.db` and settings remain included.
- Two successful ~121 MiB archives verified October 15: `homepi-apps-20261015-113145.tar.gz`, `homepi-apps-20261015-114743.tar.gz`. Remote SHA-256 checks matched; first archive passed gzip test; **bare-metal restore untested**.
- `/opt/homepi-backup/retention.py` preserves the initial baseline and newest backup; 14 daily and four weekly age buckets. Default dry-run, automatic `--apply` after successful remote checksum verification; aged deletions still untested.
- **Pending:** Create HomePi backup Push monitor via existing **Servius Uptime Kuma WebUI**, wire success-only heartbeat after complete verification/retention, test alerts.

### Safe diagnostic commands

```bash
systemctl list-timers --all --no-pager | grep homepi
systemctl show homepi-backup.service -p Result -p ExecMainStatus
sudo journalctl -u homepi-backup.service -n 35 --no-pager -o cat
sudo bash -n /opt/homepi-backup/daily.sh
```

See [important directories](../Operations/Important-Paths.md), [backup runbook](../Operations/HomePi-Backup-and-Restore.md), and [changelog](../CHANGELOG.md).

## To document next

- Current IP and Tailscale setup.
- Which node is primary/secondary DNS.
- Backup location and restoration instructions.
- Actual installed services, versions and where their configuration is stored.
