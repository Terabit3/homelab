# Dashy

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** User Compose project `dashy`, container `Dashy` observed healthy Oct 14.

## Role
Alternative web dashboard/shortcut portal.

## Dependencies and data
Homepage overlaps its purpose; preserve dashboard configuration while deciding which to keep.

## Operator checks
Verify widgets, URLs and status endpoints before retiring in favor of Homepage. Check Compose and bind mounts through Dockhand; verify static config survives container replacement.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, and targeted `docker inspect --format` of health and mounts. For TrueNAS-managed `ix-*` apps, use TrueNAS Apps for management rather than an independent Dockhand recreation.

## Recovery and change control
Check `zpool status`, confirmed backups and deployment ownership before updates. Retain application configuration and dependent databases. Verify app functionality, proxy access, and monitoring after any changes. Status above describes Oct 2026 evidence—not a live guarantee.

## References
[Application directory](../README.md) · [Services inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

No credentials or populated environment files in Git.
