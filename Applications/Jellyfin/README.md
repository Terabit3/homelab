# Jellyfin

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** TrueNAS-managed `ix-jellyfin-jellyfin-1` image `ghcr.io/jellyfin/jellyfin:12.2` observed healthy Oct 14.

## Role
Local playback server for media library; media acquisition services are separate.

## Dependencies and data
Media resides on `praesidium`, and access depends on the configured mount and permissions; transcoding hardware mapping not verified in this evidence.

## Operator checks
If streaming fails, inspect server logs, direct LAN response and mount availability before assuming ARR is responsible. Changes through TrueNAS Apps, with app config/database backup and playback verification.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, targeted `docker inspect --format` of health and mounts, and `sudo zpool status`. For TrueNAS-managed `ix-*` apps, manage through TrueNAS Apps.

## Recovery and change control
Confirm owner, actual data paths and recoverable backups before a restart, update or migration. Verify function, proxied access and monitoring afterwards. The dated state above is not proof of current status.

## References
[Application directory](../README.md) · [Service inventory](../../Services.md) · [Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

Never publish credentials or live environment files.
