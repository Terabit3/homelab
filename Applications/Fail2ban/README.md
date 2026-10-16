# Fail2ban

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** User Compose project `fail2ban`, container `fail2ban` observed healthy Oct 14. Group permissions on Compose files corrected later.

## Role
Bans repeat offenders based on monitored logs.

## Dependencies and data
Effectiveness depends on actual log sources, reverse-proxy real-client IP handling, firewall/nftables privileges, and jail configuration.

## Operator checks
Read active jail/status and recent bans, verify logs and ability to enforce blocks. Back up jails/action definitions; do not assume a healthy container means bans reach the host firewall.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, and targeted `docker inspect --format` of health and mounts. For TrueNAS-managed `ix-*` apps, use TrueNAS Apps for management rather than an independent Dockhand recreation.

## Recovery and change control
Check `zpool status`, confirmed backups and deployment ownership before updates. Retain application configuration and dependent databases. Verify app functionality, proxy access, and monitoring after any changes. Status above describes Oct 2026 evidence—not a live guarantee.

## References
[Application directory](../README.md) · [Services inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

No credentials or populated environment files in Git.
