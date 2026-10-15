# Servius — System Reference

> **Source:** legacy homelab documentation. Hardware details are historical records; verify against the current server before relying on them. Container status and versions belong in `Snapshots/Docker-Inventory.md`.

## Purpose

Servius is a TrueNAS SCALE system used for ZFS storage, Docker applications, family media, and backups.

## Hardware (legacy inventory)

| Component | Recorded specification |
|---|---|
| CPU | Intel Xeon E-2246G |
| CPU cooler | Noctua NH-D15 |
| Motherboard | Supermicro X11SCA-F |
| RAM | 2 × 32 GB Kingston KSM26ED8/32ME DDR4-2666 ECC |
| Boot drive | Samsung 870 EVO 500 GB SSD |
| Fast application storage | Samsung 990 PRO 2 TB NVMe (`speedpool`) |
| Hard drives | 4 × HGST Ultrastar He12 12 TB HUH721212ALE601 |
| Case | Fractal Design Define 7 |
| Power supply | ASUS ROG Strix 650 W Gold |
| OS | TrueNAS SCALE |

## Important storage locations

- `/mnt/speedpool/appdata` — application configuration and fast storage (historical path; confirm per app).
- `/mnt/praesidium` — bulk storage pool.
- `/mnt/speedpool/appdata/stacks` — known Docker Compose stack files (confirm current layout).

## Useful maintenance reminders

- Verify ZFS pool health and snapshot tasks in TrueNAS before making changes.
- Keep independent copies of critical application data and the TrueNAS configuration.
- Use the Supermicro BMC/IPMI for hardware monitoring and out-of-band administration.
- Record verified restoration steps and outcomes alongside each app.

## Current application inventory

See [`../Snapshots/Docker-Inventory.md`](../Snapshots/Docker-Inventory.md), rather than the old service list. The old list included programs such as Plex, Homarr, and Portainer that were **not confirmed** in the October 2026 Docker inventory; it also omitted newer apps. This is not evidence that those services were uninstalled.

## To verify later

- TrueNAS SCALE release and current boot environment.
- Network interfaces and BMC address (keep private addresses in a separate approved network reference if desired).
- Storage topology, pool encryption, health, and dataset ownership.
- Whether hardware components have changed since this inventory.
