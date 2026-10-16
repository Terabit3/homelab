# Authentik

## Purpose
Identity provider and single sign-on gateway for web applications. Review proxy authentication flows and application access policies.

## Servius / homelab context
TrueNAS/Docker ecosystem; exact instance configuration and protected app list require live verification.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Confirm reverse proxy integration, admin recovery method, database backup, and exposure before policy changes.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
