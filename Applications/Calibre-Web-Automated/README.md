# Calibre-Web-Automated

## Hosts
**Servius (TrueNAS SCALE).** TrueNAS-managed app.

## Purpose
Web interface and automated book library ingestion and metadata management.

## Servius / homelab context
TrueNAS-managed application was observed running in the Oct 2026 health checks.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Check books/media mount, import flow, database backup, and user authentication.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
