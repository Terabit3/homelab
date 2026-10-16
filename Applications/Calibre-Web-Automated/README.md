# Calibre-Web-Automated

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** TrueNAS-managed `ix-calibre-web-automated-calibre-web-automated-1`; `ghcr.io/crocodilestick/calibre-web-automated:v4.0.8` observed healthy Oct 14.

## Role
Web ebook library/catalog and automated book ingestion.

## Dependencies and data
Ebook storage and application database; exact mounts must be inspected live.

## Operator checks
Inspect TrueNAS Apps status, import folders, metadata changes and library visibility. Preserve its database and book originals before updates; manage via TrueNAS Apps.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, and targeted `docker inspect --format` of health and mounts. For TrueNAS-managed `ix-*` apps, use TrueNAS Apps for management rather than an independent Dockhand recreation.

## Recovery and change control
Check `zpool status`, confirmed backups and deployment ownership before updates. Retain application configuration and dependent databases. Verify app functionality, proxy access, and monitoring after any changes. Status above describes Oct 2026 evidence—not a live guarantee.

## References
[Application directory](../README.md) · [Services inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

No credentials or populated environment files in Git.
