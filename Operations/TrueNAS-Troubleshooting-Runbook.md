# TrueNAS SCALE — Troubleshooting & Recovery Runbook

> **For Servius.** Written October 2026 against TrueNAS SCALE **24.10 documentation** and the operator's October 14 system notes. **Installed TrueNAS version and live pool health must be checked before taking action.** This is a reference, not an instruction to perform maintenance now.
>
> **Before changing storage:** capture the error, check backups, identify disks by **serial number**, and confirm which pool is affected. Do not use `zpool clear`, force a replacement, delete a pool, or roll back a dataset just to make a warning disappear.

## 1. First five minutes: diagnose without changing anything

SSH into Servius or use the TrueNAS Shell. Most commands here are read-only.

```bash
hostname
sudo zpool status -v
sudo zpool list
sudo zfs list -o name,used,avail,mountpoint
lsblk -o NAME,SIZE,MODEL,SERIAL,TYPE,MOUNTPOINTS
sudo journalctl -p err -b --no-pager -n 80
```

Look in **TrueNAS → Alerts**, then **Storage → Pools**. Save the full error message and date in a troubleshooting note. `zpool status -v` distinguishes device errors from permanent errors in specific files. On a degraded pool, **do not start a long scrub or deliberately offline another disk until you understand the failure and remaining redundancy**.

**What each pool means on Servius** (historically recorded, not live-verified):

| Pool | Layout | Risk / response |
|---|---|---|
| `praesidium` | Four HGST 12 TB drives, RAIDZ2 | Two-drive parity protects against up to two failed members of the same RAIDZ2 vdev, but not every conceivable correlated failure. Check cabling and disk identity before replacement. |
| `speedpool` | Single 2 TB Samsung 990 Pro NVMe | No ZFS redundancy. If the device fails, restore appdata and other stored data from independently verified backups. Snapshots on the same NVMe are **not** backup copies. |
| Boot pool | Samsung 870 EVO 500 GB | Reinstall TrueNAS onto replacement boot media and restore the saved system configuration, after verifying compatibility. Data pools live separately. |

### Quick decision table

| Symptom | Check first | Avoid |
|---|---|---|
| Pool **DEGRADED** | `zpool status -v`, affected disk serial, cable/power path, alerts | Pulling disks by Linux name alone; forcing online/offline blindly |
| Pool **FAULTED/UNAVAIL** | Full topology, whether disks are physically visible, backups | Creating a new pool over old disks; `zpool destroy` |
| Read/write/checksum errors | Disk SMART, connectors/cables, previous scrub, kernel log | Immediately clearing errors without investigating cause |
| Single NVMe vanishes | `dmesg`, `journalctl -k`, PCIe/device visibility | Repeated writes or stressful tests on an unstable pool |
| Pool full | `zfs list`, snapshots, quotas/reservations, application consumption | Deleting random ZFS snapshots or app directories |
| Web UI unreachable | Ping/SSH, interface/IP, router/DNS, console display | Resetting TrueNAS to defaults as the first step |
| Apps won't start | Pool mount, app dataset, Docker logs, dependency status | Removing volumes or reinstalling an app before backing up |

## 2. A pool is degraded or a drive disappears

1. **Write down the pool and member:** run `sudo zpool status -v`. Check whether the status is `DEGRADED`, `FAULTED`, `OFFLINE`, `REMOVED` or `UNAVAIL` and whether a resilver is active.
2. **Check whether the drive is physically detected:** `lsblk -o NAME,SIZE,MODEL,SERIAL,TYPE` and TrueNAS **Storage → Disks**. Match **model and serial** with the affected vdev member. `/dev/sdX` ordering can change after a reboot.
3. **Check the connection:** a missing disk can be caused by a SATA data cable, **power cable**, power splitter, HBA/backplane, or the disk itself. Power down before reseating non-hot-swap connectors; never pull an unknown member.
4. **Check SMART** on a drive that is present: `sudo smartctl -a /dev/disk/by-id/<disk-id>` (replace with the verified disk ID). Look for failed tests, pending/reallocated sectors, transport errors and trends. SMART alone cannot certify a drive healthy.
5. **Confirm backups** and the number of healthy members. If the condition could be a loose cable, correct that first; avoid buying/replacing a healthy disk on assumptions alone.
6. If the disk is actually failed, use [Disk Replacement & Expansion](TrueNAS-Disk-Replacement-and-Expansion.md). **Do not offline a second RAIDZ2 member while the pool is already under stress without reviewing the topology.**
7. After any repair, confirm pool `ONLINE`, zero newly accumulating errors, and that applications/shares work. Document the disk serial change and cause.

> **Servius-specific historical issue:** `praesidium` previously had a missing power connection; repair that connection and recheck the pool before classifying the affected disk as failed. This note does **not** mean the issue is still present today.

## 3. SMART, checksum errors and scrub findings

```bash
sudo zpool status -v
sudo zpool events -v | tail -n 100
sudo smartctl -a /dev/disk/by-id/<verified-id>
sudo journalctl -k -b --no-pager | grep -Ei 'ata|scsi|nvme|reset|i/o error|zfs' | tail -n 100
```

- **READ/WRITE** counters can indicate failed I/O, cable/power problems or a failing disk.
- **CKSUM** errors mean ZFS received data that failed an integrity check; investigate hardware, controller, memory and cabling as well as disk media.
- **Scrub repaired errors:** ZFS reconstructed affected data where redundancy permitted. That does **not** prove the hardware cause is gone.
- **Permanent errors / affected file names:** preserve the report and check a verified backup; some affected data may need restoration.
- **A scrub is not a backup and cannot recover unavailable data if redundancy and surviving copies are insufficient.**

For a healthy, stable pool, TrueNAS **Storage → pool → Scrub** runs an integrity scan; scheduled tasks appear in **Data Protection**. Avoid starting or piling additional heavy I/O onto an unstable or degraded pool without determining whether to replace a disk first. **Never use `zpool clear` to claim a fault is resolved**; only clear historical counters after solving the underlying problem and retaining diagnostic notes.

## 4. NVMe controller reset, vanished `speedpool`, or appdata unavailable

Recognizable historical symptoms:

```text
nvme nvme0: controller is down; will reset
CSTS=0xffffffff, PCI_STATUS=0xffff
failed to read NVMe SMART/Health Information
```

1. Stop nonessential write-heavy applications if the NVMe is intermittent; preserve logs and check independent backups.
2. Check `sudo zpool status -v speedpool`, `lsblk`, and `sudo journalctl -k -b --no-pager | grep -i nvme`.
3. Investigate heat, drive firmware, seating, motherboard BIOS, power and PCIe reliability.
4. An **older documented workaround** was `nvme_core.default_ps_max_latency_us=0 pcie_aspm=off` in TrueNAS kernel options. This alters power-management behavior system-wide. **Don't reapply it blindly**; verify whether it's already configured and whether the problem is recurring on the current TrueNAS release.
5. If `speedpool` is irrecoverable, rebuild/replace that storage and restore the **verified** appdata/VM data copies. Confirm database consistency and configuration ownership; do not assume file-level Syncthing of a live database is a restorable database backup.

See the separate historical guide `Troubleshooting/TrueNAS-NVMe-Controller-Reset.md` if present.

## 5. A dataset or share is missing

```bash
sudo zpool status -v
sudo zfs list -o name,mounted,mountpoint,canmount
sudo zfs get -r mountpoint,canmount,mounted praesidium
```

Check whether the **pool is imported**, the **dataset is mounted**, its **mountpoint is expected**, the share is enabled, and network access/permissions are working. TrueNAS **Datasets**, **Shares**, and **System → Services** are safer starting points than manually changing ACLs from the shell.

If clients see `permission denied`, identify the exact user/group and affected path; check dataset ACL and SMB/NFS share settings. Avoid recursive `chmod -R 777` or broad `chown -R` on managed appdata.

## 6. Disk capacity, snapshots and 'out of space'

```bash
sudo zpool list
sudo zfs list -o name,used,avail,refer,usedbysnapshots,mountpoint
sudo zfs list -t snapshot -o name,used,creation | tail -n 40
```

- Check **dataset usage**, **snapshot-held space**, **quotas/reservations**, and actual free pool capacity separately.
- Deleting a file may not free space while a snapshot still references it.
- Prefer checking TrueNAS **Data Protection → Periodic Snapshot Tasks** before deleting snapshots.
- Never delete a snapshot used as the base for replication without understanding the consequences.
- Leave meaningful headroom, especially before large app updates, replications, or resilvers. A 100%-full ZFS pool can become difficult to administer.

## 7. Recover an accidentally deleted or modified file

1. **Stop writing** to the affected location when practical.
2. Identify the correct dataset and snapshot in **Datasets → Snapshots**.
3. Prefer **browse/copy the file** from `.zfs/snapshot/<snapshot-name>/...` or **clone** a snapshot to a temporary dataset and copy needed items back.
4. Verify restored contents and permissions.
5. **Avoid rolling back the entire dataset** for one file. Rollback can discard all subsequent changes and newer snapshots, and may affect incremental replication.
6. If no snapshot contains a good copy, use an independent backup (Syncthing versioning, offsite backup, archived ZFS replication, etc.).

## 8. TrueNAS won't boot / boot SSD failed

1. Confirm hardware, BIOS/UEFI boot order and the boot SSD are recognized. Record the displayed error.
2. If the problem followed an OS upgrade, check **System → Boot / Boot Environments** or the boot menu for a previous boot environment. This rolls back the OS environment, **not** pool contents or application data.
3. If the boot media is dead, install a compatible TrueNAS SCALE release onto a **verified replacement boot SSD**. Be careful not to select a data-pool member as the installer target.
4. Restore the previously saved TrueNAS system configuration using **System → General Settings → Manage Configuration** on 24.10 (menus can change in later releases). Prefer the backup that includes **Export Password Secret Seed**, stored privately.
5. Import the existing data pools only after confirming disk identity and pool health. Do **not** create new empty pools with the old member disks.
6. Check network settings, shares, apps, VM definitions, encryption keys, scheduled tasks and credentials. Verify services one by one.

TrueNAS system configuration exports **do not replace dataset backups**. Keep encrypted dataset keys/passphrases and configuration backups in the private vault, with a second independent protected copy.

## 9. Web UI, DNS or networking fails

- Try the known LAN address directly (as last verified: `192.168.50.121`), then SSH and the physical/IPMI console if needed.
- Test whether the fault is **network**, **DNS**, **Nginx Proxy Manager**, **Tailscale**, or the TrueNAS web service. A proxy failure can make an otherwise healthy service look offline.
- On Servius, read-only checks:

```bash
ip -br addr
ip route
sudo ss -lntp | head -n 45
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}'
```

- Avoid resetting networking remotely without out-of-band console access. Keep the server's BMC/IPMI method documented, but **never commit credentials**.

## 10. Docker application / dataset / VM doesn't start

1. Check `zpool status -v` first if the app's data is on `speedpool` or `praesidium`.
2. Find the managing owner: **TrueNAS Apps** (`ix-*`) or **separate Compose stack** (Dockhand or other manager). Avoid competing deployments.
3. Review `docker logs --tail 150 <name>`, health status, mounts and any database/VPN dependencies. Run logs commands on **Servius**, not Windows PowerShell.
4. Check free disk space, ACLs, network, port conflicts, image changes and referenced `.env` values in the private vault.
5. Restore individual configuration or database backups if necessary. Do not delete appdata to make a container boot.
6. For AMP, verify **Debian VM health and its network** separately from the TrueNAS Docker inventory.

## 11. Preventive checks / documenting a successful fix

- Regularly review alerts, SMART trends, scrub schedule/results, backup freshness, replication failures and pool space.
- Before major hardware/storage changes, verify an **independent** backup and save the TrueNAS configuration **with secret seed** outside GitHub.
- Document **symptom → exact error → diagnosis → what worked → how verified → date/version**.
- Include the pool name, disk model/serial, replaced part and any failed approach **without exposing private credentials**.

## Official references

These are written for the 24.10 interface; choose documentation matching the installed release after upgrading.

- [TrueNAS 24.10 — Managing Pools](https://www.truenas.com/docs/scale/24.10/scaletutorials/storage/managepoolsscale/)
- [TrueNAS 24.10 — Replacing Disks](https://www.truenas.com/docs/scale/24.10/scaletutorials/storage/disks/replacingdisks/)
- [TrueNAS 24.10 — Managing System Configuration](https://www.truenas.com/docs/scale/24.10/scaletutorials/systemsettings/general/managesysconfigscale/)
- [TrueNAS 24.10 — Boot Pool Management](https://www.truenas.com/docs/scale/24.10/scaletutorials/systemsettings/managebootenvironscale/)
- [TrueNAS 24.10 — Snapshot Management](https://www.truenas.com/docs/scale/24.10/scaletutorials/datasets/managesnapshots/)
