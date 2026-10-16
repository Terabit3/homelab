# Immich

## Hosts
**Servius (TrueNAS SCALE).** TrueNAS-managed image/media library.

## Purpose
Self-hosted photo and video organization with indexing, mobile backup and thumbnail generation.

## Servius / homelab context
TrueNAS-managed app running in October 2026 inventory; images, uploads and database have separate dataset/snapshot considerations.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Before updates or pruning, verify database-consistent backups and recoverable originals; sync alone does not guarantee a restore.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
