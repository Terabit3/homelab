# Servius Docker Inventory

Last exported: 2026-10-14

This is a snapshot of the containers on Servius.

## arr-stack

| Container | Image | Status |
|---|---|---|
| qbittorrent | linuxserver/qbittorrent:latest | Up 24 hours |
| gluetun | qmcgaw/gluetun:latest | Up 24 hours (healthy) |
| prowlarr | lscr.io/linuxserver/prowlarr:latest | Up 24 hours |
| sonarr | lscr.io/linuxserver/sonarr:latest | Up 24 hours |
| radarr | lscr.io/linuxserver/radarr:latest | Up 24 hours |
| flaresolverr | ghcr.io/flaresolverr/flaresolverr:latest | Up 24 hours |

## arr-stack-secondary

| Container | Image | Status |
|---|---|---|
| bazarr | lscr.io/linuxserver/bazarr:latest | Up 24 hours |
| houndarr | ghcr.io/av1155/houndarr:latest | Up 24 hours (healthy) |
| unpackerr | golift/unpackerr | Up 24 hours |
| cleanuparr | ghcr.io/cleanuparr/cleanuparr:latest | Up 24 hours |
| jellyseerr | ghcr.io/seerr-team/seerr:latest | Up 24 hours |

## arr-stack-stats

| Container | Image | Status |
|---|---|---|
| jellystat | cyfershepard/jellystat:latest | Up 28 hours (healthy) |
| jellystat-db | postgres:15.2 | Up 28 hours |
| wizarr | ghcr.io/wizarrrr/wizarr:latest | Up 28 hours (healthy) |

## authentik

| Container | Image | Status |
|---|---|---|
| authentik-server-1 | ghcr.io/goauthentik/server:2026.8.3 | Up 21 hours (healthy) |
| authentik-worker-1 | ghcr.io/goauthentik/server:2026.8.3 | Up 21 hours (healthy) |
| authentik-postgresql-1 | postgres:16-alpine | Up 21 hours (healthy) |

## boxarr

| Container | Image | Status |
|---|---|---|
| boxarr | ghcr.io/iongpt/boxarr:latest | Up 26 hours (healthy) |

## byparr

| Container | Image | Status |
|---|---|---|
| byparr | ghcr.io/thephaseless/byparr:latest | Up 26 hours (healthy) |

## dashy

| Container | Image | Status |
|---|---|---|
| Dashy | lissy93/dashy | Up 28 hours (healthy) |

## fail2ban

| Container | Image | Status |
|---|---|---|
| fail2ban | crazymax/fail2ban:latest | Up 23 hours (healthy) |

## homepage

| Container | Image | Status |
|---|---|---|
| homepage | ghcr.io/gethomepage/homepage:latest | Up 20 hours (healthy) |

## ix-arr-dashboard

| Container | Image | Status |
|---|---|---|
| ix-arr-dashboard-arr-dashboard-1 | ghcr.io/kha-kis/arr-dashboard:2.24.1 | Up 27 hours (healthy) |

## ix-calibre-web-automated

| Container | Image | Status |
|---|---|---|
| ix-calibre-web-automated-calibre-web-automated-1 | ghcr.io/crocodilestick/calibre-web-automated:v4.0.8 | Up 24 hours (healthy) |

## ix-dockhand

| Container | Image | Status |
|---|---|---|
| ix-dockhand-dockhand-1 | fnsys/dockhand:v1.0.53 | Up 24 hours (healthy) |

## ix-filebrowser-quantum

| Container | Image | Status |
|---|---|---|
| ix-filebrowser-quantum-filebrowser-quantum-1 | ghcr.io/gtsteffaniak/filebrowser:2.0.0-stable | Up 3 hours (healthy) |
| ix-filebrowser-quantum-init-1 | mikefarah/yq:4.54.1 | Exited (0) 3 hours ago |
| ix-filebrowser-quantum-permissions-1 | ixsystems/container-utils:1.0.2 | Exited (0) 3 hours ago |

## ix-immich

| Container | Image | Status |
|---|---|---|
| ix-immich-server-1 | ghcr.io/immich-app/immich-server:v3.3.1 | Up 46 hours (healthy) |
| ix-immich-pgvecto-1 | ghcr.io/immich-app/postgres:18-vectorchord1.1.1 | Up 46 hours (healthy) |
| ix-immich-pgvecto_upgrade-1 | ixsystems/postgres-upgrade:1.2.22 | Exited (0) 46 hours ago |
| ix-immich-machine-learning-1 | ghcr.io/immich-app/immich-machine-learning:v3.3.1 | Up 46 hours (healthy) |
| ix-immich-redis-1 | valkey/valkey:9.1.2 | Up 46 hours (healthy) |
| ix-immich-permissions-1 | ixsystems/container-utils:1.0.2 | Exited (0) 46 hours ago |

## ix-jellyfin

| Container | Image | Status |
|---|---|---|
| ix-jellyfin-jellyfin-1 | ghcr.io/jellyfin/jellyfin:12.2 | Up 44 hours (healthy) |
| ix-jellyfin-permissions-1 | ixsystems/container-utils:1.0.2 | Exited (0) 44 hours ago |

## ix-nginx-proxy-manager

| Container | Image | Status |
|---|---|---|
| ix-nginx-proxy-manager-npm-1 | jc21/nginx-proxy-manager:2.16.0 | Up 5 days (healthy) |

## ix-pihole

| Container | Image | Status |
|---|---|---|
| ix-pihole-pihole-1 | pihole/pihole:2026.09.0 | Up 5 days (healthy) |

## ix-syncthing

| Container | Image | Status |
|---|---|---|
| ix-syncthing-syncthing-1 | syncthing/syncthing:2.1.6 | Up 2 hours (healthy) |

## ix-tailscale

| Container | Image | Status |
|---|---|---|
| ix-tailscale-tailscale-1 | ghcr.io/tailscale/tailscale:v1.102.5 | Up 5 days (healthy) |

## ix-uptime-kuma

| Container | Image | Status |
|---|---|---|
| ix-uptime-kuma-uptime-kuma-1 | ghcr.io/louislam/uptime-kuma:2.5.6 | Up 12 hours (healthy) |

## kuma-sync

| Container | Image | Status |
|---|---|---|
| kuma-container-sync | ghcr.io/benrhughes/kuma-container-sync:latest | Up 3 hours (unhealthy) |

## nextcloud

| Container | Image | Status |
|---|---|---|
| nextcloud-cron | nextcloud:apache | Up 25 hours |
| nextcloud | nextcloud:apache | Up 25 hours |
| nextcloud-db | postgres:17-alpine | Up 25 hours (healthy) |
| nextcloud-redis | redis:7-alpine | Up 25 hours |

## parrot

| Container | Image | Status |
|---|---|---|
| parrot | ghcr.io/aquelemiguel/parrot:latest | Exited (137) 29 hours ago |

## whisparr

| Container | Image | Status |
|---|---|---|
| whisparr | ghcr.io/hotio/whisparr:v3-pr-eros-refactor-parser-52fb0f32917ab822be95d7a91abfff196b4e35ae | Exited (0) 29 hours ago |

## windows

| Container | Image | Status |
|---|---|---|
| WinApps | ghcr.io/dockur/windows:latest | Exited (0) 25 hours ago |

