# ARR Stack — Servius

## Overview

Media automation applications running on Servius, managed through Dockhand.

## Docker stacks

| Stack | Applications |
|---|---|
| arr-stack | Sonarr, Radarr, Prowlarr, qBittorrent, Gluetun, FlareSolverr |
| arr-stack-secondary | Bazarr, Cleanuparr, Houndarr, Seerr, Unpackerr |
| arr-stack-stats | Jellystat, PostgreSQL, Wizarr |

## Where everything is stored

**Live configurations on Servius:** `/mnt/speedpool/appdata/stacks/<stack>/`

**Original backups on my PC:** `E:\Homelab-Private\Servius\<stack>\`

**GitHub documentation:** `Applications/ARR Stack/compose/<stack>/`

The real passwords, VPN keys, and secrets belong in the private backup, never in GitHub.

## Useful commands

Run these in **Servius SSH**, not Windows PowerShell.

**List ARR containers:**

`sudo docker ps --filter label=com.docker.compose.project=arr-stack`

**View recent logs:**

`sudo docker logs --tail 100 sonarr`

`sudo docker logs --tail 100 radarr`

`sudo docker logs --tail 100 prowlarr`

`sudo docker logs --tail 100 qbittorrent`

`sudo docker logs --tail 100 gluetun`

**Check Gluetun health:**

`sudo docker inspect gluetun --format '{{.State.Health.Status}}'`

## Troubleshooting

Keep a separate Markdown document for each resolved issue in the `Troubleshooting/` folder.

Each document should explain:

- What stopped working
- What caused the problem
- Exactly how I fixed it
- How I confirmed the fix
- Anything I should remember next time

## Rebuilding

1. Retrieve the original Compose and `.env` files from `E:\Homelab-Private`.
2. Restore the application data and required storage directories.
3. Check mounts, permissions, networking, and dependencies.
4. Review and deploy through Dockhand.
5. Verify applications, VPN connectivity, and media paths.

Do not overwrite existing application data without checking it first.