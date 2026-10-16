# Fail2ban

## Purpose
Log-driven banning for repeated authentication or application access failures.

## Servius / homelab context
Used in Servius security stack; earlier Dockhand file-access issue addressed via group membership.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Verify actual log sources, jail/action rules, host vs container firewall access, and proxy client-IP handling before trusting bans.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
