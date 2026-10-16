# Nginx Proxy Manager

## Hosts
**Servius (TrueNAS SCALE).** TrueNAS-managed reverse proxy.

## Purpose
Reverse proxy and TLS certificate manager for application domain routing.

## Servius / homelab context
TrueNAS app used for homelab web access alongside Authentik; detailed historical Pi-hole procedure is in Pi-hole-Proxy-Redirect.md.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Check direct app availability separately from proxy, certificate renewal and access lists. Do not expose an admin UI solely to troubleshoot routing.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
