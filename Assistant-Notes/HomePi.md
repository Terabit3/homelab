# HomePi — assistant working notes

## 2026-10-15 — Docker management migration

**Verified (user-provided terminal/UI):** HomePi on LAN `192.168.50.26`; DietPi ARM64 (`aarch64`, 64-bit userspace), Docker 29.9.0, Docker Compose v5.6.0. Root filesystem 30 GB total, 24 GB available. RAM 955 MiB total, 626 MiB available at capture; 1.1 GiB swap.

Docker containers: Uptime Kuma (`louislam/uptime-kuma:1`, host port 3001), Portainer CE (`portainer/portainer-ce:2.21.5`, ports 8000/9443), and newly installed Dockhand. Dockhand connected successfully via local Unix socket, identifying Docker 29.9.0 and 3 containers.

**Verified (Dockhand UI):** Uptime Kuma is an untracked Compose stack; detected Compose path is `/data/compose/2/docker-compose.yml`. This is a container-side Portainer path until its source mount is checked. Host location and Uptime Kuma persistent data mount are **not yet verified**. Dockhand warning: kernel cgroup memory accounting disabled, per-container memory shows 0 B.

**Planned:** Standardize future Docker Compose projects under `/opt/stacks/<app>/compose.yaml` if not already in use. Inspect existing folders first. Ensure Dockhand has the necessary bind mount to edit files. Keep Portainer and its persistent data until Uptime Kuma is safely adopted and verified.

**Pending evidence:** read-only listing of `/opt`, `/srv`, `/home/dietpi`, search for Compose files, Docker root, inspect Portainer/Uptime Kuma/Dockhand mounts, and original Compose content (remove any secrets before sharing).

**Safety:** No blind `Save & redeploy`; don't move/overwrite existing Docker data; avoid kernel boot changes while remote.

See [host docs](../Hosts/HomePi/README.md) and [verified first backup record](../Operations/HomePi-Backup-2026-10-15.md). Earlier backup archives were integrity-tested but not restore-tested.
