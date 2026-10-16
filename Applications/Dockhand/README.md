# Dockhand

## Purpose
Docker Compose stack manager for editing, deploying, and observing grouped services.

## Servius / homelab context
TrueNAS-managed container ix-dockhand-dockhand-1 runs as UID/GID 568:568. Bind mounts map /mnt/speedpool/appdata/stacks to both /appdata/stacks and the matching absolute path.

**Status:** Documentation based on previously observed configuration through 2026-10-16; not live monitoring. Verify actual app state and configuration before action.

## Operations and verification
Protect application config, Docker socket permissions and Stack ownership. Stage ARR files under stacks/staging/arr-compose-v2; eight configs validate but are NOT deployed.

Use `sudo docker ps -a` and `sudo docker inspect <container> --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'` only after confirming the exact container name. TrueNAS-managed `ix-*` apps must be operated through the TrueNAS-supported workflow, not arbitrary Compose redeployment.

## Related documentation
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations and recovery](../../Operations/README.md)

Do not commit passwords, API keys, real `.env` files or private keys.
