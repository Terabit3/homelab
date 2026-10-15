# HomePi — assistant working notes

## 2026-10-15 — Docker management migration

**Verified (user-provided terminal/UI):** HomePi on LAN `192.168.50.26`; DietPi ARM64 (`aarch64`, 64-bit userspace), Docker 29.9.0, Docker Compose v5.6.0. Root filesystem 30 GB total, 24 GB available. RAM 955 MiB total, 626 MiB available at capture; 1.1 GiB swap.

Docker containers: Uptime Kuma (`louislam/uptime-kuma:1`, host port 3001), Portainer CE (`portainer/portainer-ce:2.21.5`, ports 8000/9443), and newly installed Dockhand. Dockhand connected successfully via local Unix socket, identifying Docker 29.9.0 and 3 containers.

**Verified (Dockhand UI):** Uptime Kuma is an untracked Compose stack; detected Compose path is `/data/compose/2/docker-compose.yml`. This is a container-side Portainer path until its source mount is checked. Host location and Uptime Kuma persistent data mount are **not yet verified**. Dockhand warning: kernel cgroup memory accounting disabled, per-container memory shows 0 B.

**Planned:** Standardize future Docker Compose projects under `/opt/stacks/<app>/compose.yaml` if not already in use. Inspect existing folders first. Ensure Dockhand has the necessary bind mount to edit files. Keep Portainer and its persistent data until Uptime Kuma is safely adopted and verified.

**Pending evidence:** read-only listing of `/opt`, `/srv`, `/home/dietpi`, search for Compose files, Docker root, inspect Portainer/Uptime Kuma/Dockhand mounts, and original Compose content (remove any secrets before sharing).

**Safety:** No blind `Save & redeploy`; don't move/overwrite existing Docker data; avoid kernel boot changes while remote.

See [host docs](../Hosts/HomePi/README.md) and [verified first backup record](../Operations/HomePi-Backup-2026-10-15.md). Earlier backup archives were integrity-tested but not restore-tested.

## 2026-10-15 — filesystem inspection (verified via Termius screenshot)

- Docker root: `/var/lib/docker`.
- Existing `/opt` directories: `containerd`, `dockhand`, `pihole`. No `/opt/stacks` shown at capture.
- `/srv` contains no application directories in the displayed listing.
- User home contains `/home/dietpi/docker` and backup script `/home/dietpi/homepi-first-backup.sh`.
- Search of `/opt`, `/srv`, `/home/dietpi` to depth 5 returned no standard Compose filenames.
- Dockhand mounts: host `/opt/dockhand` -> container `/opt/dockhand`; Docker socket -> same path. `/opt/stacks` is not mounted into Dockhand yet.
- Uptime Kuma mount: host bind begins `/home/dietpi/docker/uptimek...` -> `/app/data`; screenshot clips full source path, so confirm with inspect before editing.
- Portainer volume: `portainer_data` at `/var/lib/docker/volumes/portainer_data/_data` -> `/data`. Thus original Compose file should reside at `/var/lib/docker/volumes/portainer_data/_data/compose/2/docker-compose.yml` if still present. Verify file before use.

Next: read-only `sudo ls -l /var/lib/docker/volumes/portainer_data/_data/compose/2/`, and inspect Compose file with secrets redacted. Prefer creating `/opt/stacks` for new apps; preserve existing Uptime Kuma data path and Compose project name during adoption.

## 2026-10-15 — monitoring and management roadmap

**Decision:** Replace Portainer with Dockhand on HomePi; import existing Uptime Kuma Compose stack into Dockhand without losing monitor data/history. Retire Portainer only after verified takeover and keep its volume temporarily for rollback.

**Future possibility (not approved for deployment):** Consider Beszel for lightweight system/container resource monitoring. Beszel does not provide a full replacement for Uptime Kuma's HTTP/service availability checks; compare overlapping needs before removing Kuma. Could centralize a Beszel hub on Servius with a HomePi agent to minimize Pi memory usage.

**Directory convention:** `/opt/<app-name>/` directly, without `/opt/stacks/`. Do not change original Uptime Kuma data mount blindly.

## 2026-10-15 — original Uptime Kuma Compose verified

Screenshot of HomePi command output confirms Portainer original at `/var/lib/docker/volumes/portainer_data/_data/compose/2/docker-compose.yml` (230 bytes); its service `uptime-kuma` uses image `louislam/uptime-kuma:1`, `container_name: uptime-kuma`, port `3001:3001`, `restart: always`, and bind mount `/home/dietpi/docker/uptimek:/app/data`. Existing database is independent of Portainer's stack file. Preferred copy location: `/opt/uptime-kuma/compose.yaml` (not yet confirmed created). Keep original and existing data in place until Dockhand adoption and project-label inspection.
