# Nextcloud

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** User Compose project `nextcloud`; containers `nextcloud`, `nextcloud-cron`, `nextcloud-db`, `nextcloud-redis` observed Oct 14; database healthy.

## Role
Self-hosted files, sync and collaboration.

## Dependencies and data
Dependent on application database, Redis, cron/background jobs, persistent files and trusted-domain/reverse-proxy settings.

## Operator checks
Check web login, job execution and DB/cache health. Back up database and file data consistently; preserve data directory and credentials separately; do not deploy Nextcloud with empty persistent volumes.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, targeted `docker inspect --format` of health and mounts, and `sudo zpool status`. For TrueNAS-managed `ix-*` apps, manage through TrueNAS Apps.

## Recovery and change control
Confirm owner, actual data paths and recoverable backups before a restart, update or migration. Verify function, proxied access and monitoring afterwards. The dated state above is not proof of current status.

## References
[Application directory](../README.md) · [Service inventory](../../Services.md) · [Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

Never publish credentials or live environment files.
