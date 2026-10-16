# Dockhand

**Host:** Servius / TrueNAS SCALE; HomePi / DietPi  
**Deployment (dated evidence):** Servius: TrueNAS-managed `ix-dockhand-dockhand-1` running as 568:568; HomePi: Docker-based Dockhand, previously adopted Uptime Kuma Compose in `/opt/uptime-kuma/compose.yaml`.

## Role
Compose stack management, file editing, deployment state and observation.

## Dependencies and data
Servius host binds `/mnt/speedpool/appdata/stacks` to `/appdata/stacks` and the same absolute path; Docker socket mounted. HomePi installation is independent.

## Operator checks
For Servius ARR changes, staging `staging/arr-compose-v2` has 8/8 syntactically valid projects, **not deployed**. Staged files readable by Dockhand. Never activate a duplicate Compose project or change appdata while a DB is live. On HomePi verify Uptime Kuma persistence before Portainer retirement.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, and targeted `docker inspect --format` of health and mounts. For TrueNAS-managed `ix-*` apps, use TrueNAS Apps for management rather than an independent Dockhand recreation.

## Recovery and change control
Check `zpool status`, confirmed backups and deployment ownership before updates. Retain application configuration and dependent databases. Verify app functionality, proxy access, and monitoring after any changes. Status above describes Oct 2026 evidence—not a live guarantee.

## References
[Application directory](../README.md) · [Services inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

No credentials or populated environment files in Git.
