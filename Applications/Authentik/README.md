# Authentik

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** Docker Compose project `authentik`; `authentik-server-1`, `authentik-worker-1`, `authentik-postgresql-1` were running/healthy in the Oct 14 snapshot.

## Role
Identity provider for SSO, authentication flows and access policies.

## Dependencies and data
Nginx Proxy Manager and applications protected by Authentik; PostgreSQL is essential to policy and login continuity.

## Operator checks
Inspect `docker ps -a --filter label=com.docker.compose.project=authentik`; verify login and the actual proxy auth integration. Before updates preserve PostgreSQL and authentication configuration; do not loosen access rules to solve proxy errors.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, and targeted `docker inspect --format` of health and mounts. For TrueNAS-managed `ix-*` apps, use TrueNAS Apps for management rather than an independent Dockhand recreation.

## Recovery and change control
Check `zpool status`, confirmed backups and deployment ownership before updates. Retain application configuration and dependent databases. Verify app functionality, proxy access, and monitoring after any changes. Status above describes Oct 2026 evidence—not a live guarantee.

## References
[Application directory](../README.md) · [Services inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

No credentials or populated environment files in Git.
