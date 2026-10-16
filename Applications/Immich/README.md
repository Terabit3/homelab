# Immich

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** TrueNAS-managed `ix-immich`; server, machine-learning, PostgreSQL/VectorChord and Valkey containers observed healthy Oct 14.

## Role
Photo/video backup, indexing, search and gallery management.

## Dependencies and data
Has separate originals/uploads, thumbnails, database and backup components; older snapshot review covered Immich datasets but delete was DRY-RUN ONLY.

## Operator checks
Before updating or pruning, verify recoverable originals and a consistent PostgreSQL backup. Avoid ZFS snapshot destruction or mass cleanups while `praesidium` is degraded; validate upload and browsing after changes.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, targeted `docker inspect --format` of health and mounts, and `sudo zpool status`. For TrueNAS-managed `ix-*` apps, manage through TrueNAS Apps.

## Recovery and change control
Confirm owner, actual data paths and recoverable backups before a restart, update or migration. Verify function, proxied access and monitoring afterwards. The dated state above is not proof of current status.

## References
[Application directory](../README.md) · [Service inventory](../../Services.md) · [Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

Never publish credentials or live environment files.
