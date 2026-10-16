# Dashy

## Hosts
**Servius (TrueNAS SCALE).** User-managed Compose stack.

## Purpose
Customizable homelab service dashboard and launcher.

## Servius / homelab context
User-managed Docker app; Dockhand permissions on Dashy Compose files were addressed in Oct 2026.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Locate live compose and config, confirm proxy URL, backup themes/widgets before replacement.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
