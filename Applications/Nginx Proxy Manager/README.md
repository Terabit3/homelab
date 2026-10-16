# Nginx Proxy Manager

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** TrueNAS-managed `ix-nginx-proxy-manager-npm-1` using `jc21/nginx-proxy-manager:2.16.0`, healthy Oct 14.

## Role
HTTP reverse proxy, TLS termination and access lists for user-facing services.

## Dependencies and data
Depends on DNS records, backend reachability, valid certificates, and correct source-IP behavior; Authentik may be layered on top.

## Operator checks
Compare direct backend URL with proxied domain, inspect host rules/cert expiry, and test access-list behavior from outside LAN/Tailscale. Keep the historical [Pi-hole proxy redirect](Pi-hole-Proxy-Redirect.md) only as an issue-specific procedure.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, targeted `docker inspect --format` of health and mounts, and `sudo zpool status`. For TrueNAS-managed `ix-*` apps, manage through TrueNAS Apps.

## Recovery and change control
Confirm owner, actual data paths and recoverable backups before a restart, update or migration. Verify function, proxied access and monitoring afterwards. The dated state above is not proof of current status.

## References
[Application directory](../README.md) · [Service inventory](../../Services.md) · [Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

Never publish credentials or live environment files.
