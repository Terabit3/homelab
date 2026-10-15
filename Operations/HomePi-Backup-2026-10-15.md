# HomePi first backup — October 15, 2026

HomePi Raspberry Pi 3 B+, DietPi 10.7.2 / Debian Bookworm ARM64; services Pi-hole 6, Unbound, Tailscale, Portainer, Uptime Kuma. First backup used TAR of root with quieted services, independent `/boot/firmware` archive and `sfdisk` partition map. Source gzip integrity checked, **no restore test performed**. No proven daily automation, retention or failure notifications.

Servius backup export: `/mnt/praesidium/backups/homepi/export/` (separate from the restricted account's `.ssh`). Windows replica `E:\TrueNAS Syncthing\backups\homepi`, Syncthing Receive Only; both ends reported Up to Date.

| File | SHA256 |
|---|---|
| `homepi-root-20261015-001102.tar.gz` | `533adf51aee3f2a1692fb371fd597a49f05f10df1674437e86d29d532a21ec5f` |
| `homepi-boot-20261015.tar.gz` | `2587930f1348c2f9448c8d67581a3b8a2a4f4400fb627fa1d8fd0d9aebaef4cf` |
| `homepi-partitions-20261015.sfdisk` | `512a9266d0951139fba0eb823264f70cf6e873435861e9008faf23c131e276b6` |

Before automation, inspect the one-time script for reliable service restart, SSH transfer failure, interrupted archive handling and ownership. Verify Windows hashes (`Get-FileHash`) and test restoring to spare media. **Archives contain private system secrets; never commit them to the repository.**
