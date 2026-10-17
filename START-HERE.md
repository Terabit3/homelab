# START HERE · Something is broken

**Goal: avoid turning a recoverable problem into data loss.** Follow this page before improvising. If you're stressed or inexperienced, do *only* the first three steps and ask for help using the captured output.

## 1 · Stop risky activity

- **Disk missing, pool DEGRADED/UNAVAIL, clicking drive, repeated I/O errors?** Stop bulk copying, updates, scrub initiation, reboots on a loop, and all physical drive swapping. Check pool state before touching anything.
- **Possible malware/compromise?** Preserve evidence; isolate exposure without erasing logs or blindly deleting containers.
- **Just a dashboard or web app inaccessible?** Do **not** reboot TrueNAS or reset the ZFS pool as your first move.
- **Do not click** `Wipe`, `Force`, `Delete`, `Export/Disconnect`, `Create Pool`, `Replace`, `Offline`, `Rollback`, or `Prune` until you have a verified target, consequences, and backup.

## 2 · Work out what's affected

| What you see | Open | First check |
|---|---|---|
| TrueNAS reports **DEGRADED / FAULTED / UNAVAIL** | [Pool troubleshooting](Operations/TrueNAS-Troubleshooting-Runbook.md#2-a-pool-is-degraded-or-a-drive-disappears) | `sudo zpool status -v` |
| Disk failed / need more storage | [Disk replacement and expansion](Operations/TrueNAS-Disk-Replacement-and-Expansion.md) | Pool topology + disk **serial**, not `/dev/sdX` alone |
| `speedpool` missing; NVMe errors | [NVMe incident guide](Troubleshooting/TrueNAS-NVMe-Controller-Reset.md) | `sudo zpool status -v speedpool` |
| Cannot boot TrueNAS | [Boot recovery](Operations/TrueNAS-Troubleshooting-Runbook.md#8-truenas-wont-boot--boot-ssd-failed) | Console, boot SSD detection, config backup |
| Website/app unreachable | [Application diagnosis](Operations/TrueNAS-Troubleshooting-Runbook.md#10-docker-application--dataset--vm-doesnt-start) | Direct LAN URL, host storage, container state |
| Domain fails but IP works | [Network and DNS](Operations/TrueNAS-Troubleshooting-Runbook.md#9-web-ui-dns-or-networking-fails) | DNS → proxy → service, in that order |
| Files missing / accidentally deleted | [Snapshot recovery](Operations/TrueNAS-Troubleshooting-Runbook.md#7-recover-an-accidentally-deleted-or-modified-file) | Stop writes; check snapshots before rollback |
| AMP game server unavailable | [AMP recovery](Operations/Backup-and-Restore.md#amp-games) | Debian VM/AMP instance separately from Docker |
| Alarm about failed backup | [Backup checks](Operations/Backup-and-Restore.md) | Verify last **successful** recoverable copy |

## 3 · Capture evidence (read-only)

**Run on Servius over SSH**, not Windows PowerShell. You can start with just the first command if storage may be failing.

```bash
sudo zpool status -v
sudo zpool list
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Image}}'
```

Record **when it started, what changed, the exact error, what's still working, recent backups**, and these outputs. **Redact addresses/tokens as appropriate; never share raw `.env`, API keys or full `docker inspect`.**

## 4 · Choose the smallest reversible change

1. Read the matching procedure fully before running commands.
2. Check the guide's **STOP / do not proceed** criteria.
3. Confirm you have the right system, pool, device, container or dataset.
4. Take a backup first if data could change; verify a restore path.
5. Change one thing at a time. Write down the before/after.
6. Confirm the original symptom is actually resolved; then [log the incident](Templates/Incident.md).

## Who / what can help?

For a less experienced helper, share this document and the read-only output. **If a disk serial or pool member cannot be identified confidently, stop and ask the operator or a storage professional.** Never ask a helper to guess which drive to pull.

### Critical Servius-specific facts

- `praesidium`: **historically** 4 × 12 TB RAIDZ2. A **power cable** caused a reported degraded state; a missing disk may not be dead. **Confirm live health first.**
- `speedpool`: **historically** a **single NVMe with no redundancy**. Restore depends on independent backups.
- Boot drive: Samsung 850 EVO (live verified Oct 16); **Kioxia planned mirror is unplugged/not configured** due to the cable issue. Do not assume boot mirror exists.
- AMP game services run inside a **Debian VM**, not a TrueNAS Docker container.
- The 2026-10-14 Docker list is a [snapshot](Snapshots/Docker-Inventory.md), not today's status.
