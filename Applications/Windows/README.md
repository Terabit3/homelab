# Windows VM — Dockur

## Overview

Windows environment running inside a Docker container on Servius using Dockur Windows.

**Current status:** Installed but stopped at the last Docker inventory.

**Setup documentation:** https://wiki.serversatho.me/windows

## Configuration

Docker container: `WinApps`

Docker image: `ghcr.io/dockur/windows:latest`

Compose project: `windows`

The actual configuration paths, storage mounts, Windows version, and resource allocations still need verification.

## Administrator credentials

The `windows.env` file contains the Windows administrator password.

**Private backup location:**

`E:\Homelab-Private\Servius\windows\windows.env`

This is the intended storage location. Confirm the file has been copied there before relying on it.

Never store the administrator password or the original `windows.env` file in GitHub.

## Useful commands

Run on Servius SSH.

Check container status:

`sudo docker ps -a --filter name=WinApps`

View recent logs:

`sudo docker logs --tail 100 WinApps`

Find storage locations:

`sudo docker inspect WinApps --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'`

## Rebuilding

1. Read the official Dockur Windows documentation.
2. Retrieve the saved configuration from the private vault.
3. Restore the Windows storage volume or disk image if available.
4. Verify the resource allocation, networking, and administrator credentials.
5. Redeploy and test Windows access.

## Troubleshooting

Add separate guides for problems encountered and successfully resolved.

## To verify

- Actual Compose location
- Windows disk image and storage path
- Windows version
- Assigned CPU and memory
- Access method and ports
- Whether the private environment backup exists