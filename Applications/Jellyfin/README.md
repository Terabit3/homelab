# Jellyfin

## Hosts
**Servius (TrueNAS SCALE).** TrueNAS-managed media server.

## Purpose
Self-hosted media server for streaming movies, television and other local media.

## Servius / homelab context
TrueNAS-managed app separate from the user ARR Compose stacks.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Media lives on praesidium; verify playback, library mappings, transcode hardware, server config backup and app ownership. ARR outages do not necessarily stop existing Jellyfin playback.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
