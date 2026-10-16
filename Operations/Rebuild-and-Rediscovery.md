# Rebuild and rediscover the homelab

**For two situations:** (1) the homelab is still running but you do not remember how it was built; (2) hosts, disks or applications must be reconstructed. This complements normal day-to-day [Operations](README.md). It is **not** a deploy script or substitute for independently stored backups and secrets.

## 1. Orient yourself: which machine does what?

| System | Physical or virtual? | Function | More information |
|---|---|---|---|
| **Servius** | Physical TrueNAS SCALE server (Supermicro X11SCA-F / Xeon E-2246G / 64 GB ECC) | ZFS storage, TrueNAS Apps, user Docker Compose, media, backups, VMs | [Host reference](../Infrastructure/Servius.md) · [hardware inventory](../Infrastructure/Hardware/Hardware-Inventory.md) |
| **HomePi** | Physical Raspberry Pi 3B+, DietPi | Pi-hole/Unbound DNS, Tailscale residential routing, HomePi-local Docker, backups | [Host reference](../Infrastructure/homepi.md) · [backup runbook](HomePi-Backup-and-Restore.md) |
| **h711nt** | Physical Windows workstation | Local work, private configuration vault, Syncthing backup replicas | [Path reference](Important-Paths.md) |
| **AMP Debian VM** | VM on Servius, **not another physical server** | AMP game hosting and backup sync | [Backup reference](Backup-and-Restore.md#amp-games) |


## 2. Preserve what remains before attempting a rebuild

- If hardware boots, gather **read-only evidence** first: host names, ZFS pool state, network interfaces, Docker and VM lists, application mounts, dataset names and backup dates.
- Identify disks by **serial number** and pool membership. Do **not** initialize, format, export, replace, wipe, or force-import any disk while its identity/data remain uncertain.
- As of the Oct 16, 2026 capture, `speedpool` is a **single NVMe**, no redundancy. `praesidium` is 4 × 12 TB RAIDZ2, **DEGRADED** with one unavailable member. These are historical findings; live state must be checked.
- Preserve the current TrueNAS configuration and encrypted secret seed if available. A config export does **not** contain user media or complete application datasets.
- Before changing anything, consult [START-HERE](../START-HERE.md), [disk repair](TrueNAS-Disk-Replacement-and-Expansion.md) and [backup map](Backup-and-Restore.md).

### Safe starting inventory (on an already operating Servius)

```bash
hostname
sudo zpool status -v
sudo zfs list -o name,mountpoint,used,avail
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Label "com.docker.compose.project"}}'
```

These commands do not intentionally print credentials. They are not suitable as proof of healthy backups; inspect backup logs and sample restore results separately.

## 3. Find the pieces required to reconstruct it

| Required piece | Where to look | Limitation |
|---|---|---|
| Hardware identification, ZFS topology | [Hardware](../Infrastructure/Hardware/Hardware-Inventory.md), [Servius](../Infrastructure/Servius.md) | Confirm actual serial numbers, cabling, BIOS and disk health |
| Known data and Compose file locations | [Important paths](Important-Paths.md) | Locations may have changed after the snapshot |
| Host/app and ownership list | [Services](../Services.md), [Applications](../Applications/README.md) | Distinguish TrueNAS-managed `ix-*` Apps from user Docker Compose |
| Compose definitions/examples | [ARR Compose examples](../Applications/ARR%20Stack/compose/), [Homepage](../Applications/Homepage/) | Repository examples may not match the latest production configuration |
| Application databases/media | TrueNAS datasets and **independent verified backups** | GitHub is not an appdata backup |
| Private credentials, populated .env, recovery keys | Operator's **private vault**, historically `E:\\Homelab-Private\\Servius\\` on h711nt | Vault completeness and accessibility require verification; never upload secrets to GitHub |
| TrueNAS settings export with secret seed | Offline, private configuration backup | Current valid export not verified |
| HomePi archives | [HomePi backup and restore](HomePi-Backup-and-Restore.md) | Application archives and initial full-system tarballs are distinct; bare-metal restore untested |
| AMP game archives | [Backup and restore](Backup-and-Restore.md#amp-games) | Hash-matched copies existed; actual world restore untested |

**If any required piece is missing, record the gap rather than substituting guessed paths, default credentials or empty databases.**

## 4. Suggested reconstruction dependency order

This is a **planning order**, not an instruction to deploy without backups:

1. **Hardware and storage:** Restore host bootability, identify pools and datasets; preserve originals and confirm capacity, mounts and permissions.
2. **Host networking:** Establish LAN connectivity, controlled remote SSH access, DNS source(s) and time synchronization without breaking a working DNS/route path.
3. **Management and data access:** Restore TrueNAS Apps management; only then Docker/Compose owner paths and supporting persistent files. Restore Dockhand where appropriate, but do not deploy two owners for one stack.
4. **Identity, proxy and monitoring:** Restore Authentik, Nginx Proxy Manager, Tailscale and Uptime Kuma where needed, checking each security boundary.
5. **Core data services:** Restore individual database-backed apps only from consistent database+file backups, respecting each app's schema/version and bind mounts.
6. **Media pipeline:** Jellyfin/Immich and ARR integrations. The ARR cutover described in [Servius handoff](../Assistant-Notes/Servius.md) is **staged, not deployed** as of the documented capture. Existing qBittorrent is bound to Gluetun's network namespace; preserve seeded torrents and forwarded-port behavior.
7. **AMP VM and games:** Reconstruct the VM and each game instance; validate saved worlds independently of Docker apps.
8. **HomePi independent services and backups:** Restore DNS/VPN carefully; verify clients can resolve DNS and reach management systems; then backup timers, alerts and retention.

If rebuilding from a surviving machine rather than scratch, **compare reality with this ordering and skip already healthy steps**.

## 5. Proof that recovery actually worked

For each host/service, document:
- Restored from **which exact backup and date**; host and destination; app version and config path.
- Data verification: file counts, ownership, application/database integrity and a functional login/use-case test.
- Dependent integrations: DNS/proxy/Tailscale, shared mounts, API connectivity and notifications.
- Backups following recovery: a **new** successful backup and a restore test, not only job scheduling.
- Remaining unknowns and rollback location.

A green container status alone is **not** a verified restoration. A Git repository and Sync copies of live SQLite/PostgreSQL directories alone are **not** reliable disaster recovery.

## 6. Physical 3D printers

Printer hardware, printing workflows and reconstruction steps are documented separately in [3D Printers](../3D-Printers/README.md). Record the actual machine model, slicer profiles, firmware, modifications and print-file backup locations before relying on a recovery procedure.

## 7. Maintain this manual as the homelab changes

- Update [Applications](../Applications/README.md) with **host designation and management owner** when a service is installed/moved.
- Update [Important paths](Important-Paths.md) when actual live config, database, mount or backup location changes.
- Update [Backup and restore](Backup-and-Restore.md) after a **verified restore**, including tested commands and limits.
- Log observed incidents in [CHANGELOG](../CHANGELOG.md); keep old evidence in [Archive](../Archive/README.md), not scattered through routine quick references.
- Do not commit private secrets, raw environment files, SSH keys, backup archives or sensitive appdata.

_Last compiled Oct 16, 2026 from dated notes. Treat live host checks as authoritative._
