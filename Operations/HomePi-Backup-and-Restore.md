# HomePi backup and restoration runbook

**Evidence:** Verified October 15, 2026. Full restoration is still untested.

## Locations and schedule
- Raspberry Pi DietPi: `/opt/homepi-backup/daily.sh`; `/opt/homepi-backup/retention.py`; original pre-fix `daily.sh.bak`.
- systemd: `/etc/systemd/system/homepi-backup.service` and `homepi-backup.timer`. Schedule ~03:30 local Mountain with 5-minute randomized delay and Persistent catch-up. Service timeout 300 seconds; `flock` prevents overlap.
- Secret SSH configuration in `/home/dietpi/homepi-first-backup.sh`. Never copy credentials or contents to Git.
- Servius: `/mnt/praesidium/backups/homepi/homepi-backup/`. Remote archives `homepi-apps-YYYYMMDD-HHMMSS.tar.gz`.

## Verified stages
1. Copy Pi-hole, Unbound, Tailscale, Docker/Dockhand and Kuma configuration into temporary staging.
2. Snapshot `/etc/pihole/gravity.db`, `/opt/dockhand/db/dockhand.db`, `/home/dietpi/docker/uptimek/kuma.db` using SQLite online backup and `PRAGMA quick_check`.
3. Create compressed tarball, transfer over SSH to temporary `.partial`, rename, and verify remote SHA-256.
4. Run remote retention `--apply` after checksum confirmation. Keep 14 daily, four weekly age buckets, initial `homepi-apps-20261015-113145.tar.gz` and newest copy; expired deletion still **not tested**.
5. Emit success via journal; monitoring Push URL still **not configured**.

**Excluded:** live `pihole-FTL.db` query history. Its snapshot consumed >30 minutes and repeated I/O; user query history is not included in current archives.

Verified successful October 15 archives: `homepi-apps-20261015-113145.tar.gz` and `homepi-apps-20261015-114743.tar.gz`, about 121 MiB each; remote SHA-256 matched. First archive `gzip -t` passed and contained expected configuration. No actual restoration has been performed.

## Safe inspection
```bash
# HomePi
systemctl list-timers --all --no-pager | grep homepi
systemctl show homepi-backup.service -p ActiveState -p Result -p ExecMainStatus
sudo journalctl -u homepi-backup.service -n 40 --no-pager -o cat
# Servius
sudo ls -lh /mnt/praesidium/backups/homepi/homepi-backup/homepi-apps-*.tar.gz
```

## Restore test procedure — planned (not yet validated)
1. Locate a known-good archive, verify checksum and gzip integrity, and preserve another copy.
2. Extract **only into an isolated staging directory** on a separate test target. Do not untar over `/` or live services.
3. Check expected files, user/group IDs, permissions, application versions and SQLite `quick_check`.
4. On an isolated compatible DietPi host/VM, stop only the test application, install restored config/database files, start services one at a time and test DNS, VPN and web UI.
5. Record actual commands, outcomes and recovery-time objective when a restore is proved.

## Remaining work
- [ ] Test controlled restoration and aged archive deletion.
- [ ] Create Push monitor on existing Servius Uptime Kuma WebUI, success-only heartbeat after checksum and retention; test alerts. Never commit Push URL.
- [ ] Verify first overnight unattended job and reboot catch-up.
- [ ] Reevaluate five-minute timeout as archives grow.

## Historical first full-system backup (October 15, 2026)

This predates the automated application backups described above and is a **separate** root/boot/partition-map backup workflow. HomePi was Raspberry Pi 3B+ running DietPi 10.7.2 / Debian Bookworm ARM64. A one-time root TAR (services quieted), independent `/boot/firmware` archive and `sfdisk` partition map were produced. Archive integrity checked; **no restore test**, no proven daily schedule or alerts for that *initial* full-system method. Later automated application backups have their own schedule and verification described earlier; do not conflate the two.

Export was `/mnt/praesidium/backups/homepi/export/`; Windows Syncthing Receive Only replica `E:\\TrueNAS Syncthing\\backups\\homepi` was reported Up to Date. Check current existence and hashes before restoration.

| File | SHA256 |
|---|---|
| `homepi-root-20261015-001102.tar.gz` | `533adf51aee3f2a1692fb371fd597a49f05f10df1674437e86d29d532a21ec5f` |
| `homepi-boot-20261015.tar.gz` | `2587930f1348c2f9448c8d67581a3b8a2a4f4400fb627fa1d8fd0d9aebaef4cf` |
| `homepi-partitions-20261015.sfdisk` | `512a9266d0951139fba0eb823264f70cf6e873435861e9008faf23c131e276b6` |

Before reusing this method, inspect its script's service restart and transfer-failure behavior. Archives are sensitive: **do not commit their contents or SSH credentials**. A spare-media restore test is still necessary.
