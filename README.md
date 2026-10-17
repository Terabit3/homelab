# Homelab · Servius & homepi

**A practical handbook for everyday operations AND reconstructing the homelab after memory loss, hardware failure, or data loss.** Plain Markdown; works in GitHub and VS Code without extra software.

> **Emergency?** Start at **[Something is broken → START HERE](START-HERE.md)**. Read the STOP warnings before touching storage or deleting data.
>
> **Evidence baseline:** October 16–17, 2026. This repo contains dated captures and procedures, **not live monitoring**. Check live status before acting.

**Continuing ChatGPT-assisted Servius work?** Start at **[Current handoff — Servius](Assistant-Notes/Servius.md)** for verified state, operational rules and pending ARR migration. The deployment is staged, **not complete**.

## Browse by purpose

The root README is the normal navigation entry point. For an individual app, use the [host-labelled application index](Applications/README.md). For ongoing changes, use the [Servius handoff](Assistant-Notes/Servius.md); for historic events use [CHANGELOG](CHANGELOG.md). Avoid treating dated reports or archived inventories as current state.

## Find what you need

| I need to… | Go here |
|---|---|
| Rediscover or rebuild the homelab from scratch | **[Rebuild and rediscovery](Operations/Rebuild-and-Rediscovery.md)** |
| Work out why something is broken | **[Emergency triage](START-HERE.md)** |
| Replace a disk, repair a degraded pool, add storage | [TrueNAS disk replacement & expansion](Operations/TrueNAS-Disk-Replacement-and-Expansion.md) |
| Recover a missing dataset, dead boot drive, networking or failed app | [TrueNAS troubleshooting](Operations/TrueNAS-Troubleshooting-Runbook.md) |
| Check or restore a backup | [Backup & restore](Operations/Backup-and-Restore.md) |
| Plan the ARR-to-Dockhand migration | [ARR migration manifest](Operations/ARR-Dockhand-Migration-Manifest.md) · [Data and rollback runbook](Operations/ARR-Data-Migration-Rollback.md) |
| Find an app, its config or owner | [Application directory](Applications/README.md) · [Service inventory](Services.md) |
| Document, maintain or rebuild 3D printers | [3D printers](3D-Printers/README.md) |
| Check hardware, ZFS layout or host details | [Infrastructure](Infrastructure/README.md) |
| Review changes made and their verification | **[Changelog](CHANGELOG.md)** |
| Locate important host/app/backup directories | **[Important paths](Operations/Important-Paths.md)** |
| Operate HomePi backups and plan recovery | [HomePi backup runbook](Operations/HomePi-Backup-and-Restore.md) |
| Quick Servius operational commands | [Servius quick reference](Operations/Servius-Operations-Quickref.md) |
| Track hardening and future plans | **[Roadmap / TODO](ROADMAP.md)** |
| Follow a previously diagnosed problem | [Troubleshooting cases](Troubleshooting/README.md) |
| See observed container state | [Dated Docker snapshot](Snapshots/Docker-Inventory.md) |
| Record a new problem or app | [Quick edit guide](CONTRIBUTING.md) · [Templates](Templates/) |

## Systems

- **[Servius](Infrastructure/Servius.md)** — TrueNAS SCALE, Docker applications, ZFS datasets and the Debian AMP VM.
- **[homepi](Infrastructure/homepi.md)** — Raspberry Pi; independently hosted DNS/VPN roles require live verification.
- **h711nt** — Windows workstation and a destination for copies of certain backups.

## Know before changing storage

- `praesidium`: recorded **4 × 12 TB RAIDZ2**, **DEGRADED** with one unavailable member as of October 16–17; SATA power cable replacement ordered, recovery not yet verified. Most recent read-only capacity: **51% raw allocation**, predominantly media; backups use only ~8.82 GiB.
- `speedpool`: recorded **single NVMe**, **no redundancy**; check independent backups before risky work.
- Boot: Samsung SSD; **Kioxia second boot SSD is disconnected and not yet mirrored**. See [hardware inventory](Infrastructure/Hardware/Hardware-Inventory.md).

## Documentation boundaries

Configuration examples are **not guaranteed deploy-ready**. The recorded snapshots are dated. Passwords, API keys, `.env`, private SSH material, and original sensitive configurations belong **outside Git**, in the operator's private vault. Before running a command, confirm **host, shell, device, backup and installed TrueNAS version**.

**Official references:** [TrueNAS SCALE documentation](https://www.truenas.com/docs/scale/) · [OpenZFS documentation](https://openzfs.github.io/openzfs-docs/) · [Docker Compose reference](https://docs.docker.com/compose/) · [TrueNAS Community Forums](https://forums.truenas.com/).
