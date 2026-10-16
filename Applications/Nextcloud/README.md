# Nextcloud

## Hosts
**Servius (TrueNAS SCALE).** User-managed Docker service.

## Purpose
File synchronization, collaboration and personal cloud services.

## Servius / homelab context
User-managed Docker deployment appears in Servius stack inventory.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Verify data directory, database backup, cron/background jobs, reverse proxy, trusted domains and login before upgrades.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
