# FileBrowser Quantum

## Hosts
**Servius (TrueNAS SCALE).** TrueNAS-managed file browsing app; displays mounted pools.

## Purpose
Web file browser for browsing and administering mounted TrueNAS datasets.

## Servius / homelab context
Shows mounted speedpool and praesidium storage; user uploaded ARR staging ZIP through speedpool/appdata/stacks/staging.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Access only mounted directories. Confirm mount read/write scope, permissions, authentication and recovery; never assume browsing access implies access to host /home.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
