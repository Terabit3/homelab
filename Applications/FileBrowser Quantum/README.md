# FileBrowser Quantum

**Host:** Servius / TrueNAS SCALE  
**Deployment (dated evidence):** TrueNAS-managed `ix-filebrowser-quantum-filebrowser-quantum-1` using `ghcr.io/gtsteffaniak/filebrowser:2.0.0-stable`; healthy in Oct 14 snapshot.

## Role
Browser-based file handling for mounted TrueNAS storage; separate from SSH and host shell.

## Dependencies and data
UI shows `speedpool` and `praesidium`; actual read/write capability is limited to container mounts and dataset ACLs.

## Operator checks
Previously used to upload the ARR v2 staging ZIP to `speedpool/appdata/stacks/staging`. Check mounted paths and ACLs before renaming or deleting. Verify upload destination with TrueNAS CLI; browser cannot assume access to `/home`.

**Read-only first:** `sudo docker ps -a`, `sudo docker logs --tail 100 <actual-container>`, targeted `docker inspect --format` of health and mounts, and `sudo zpool status`. For TrueNAS-managed `ix-*` apps, manage through TrueNAS Apps.

## Recovery and change control
Confirm owner, actual data paths and recoverable backups before a restart, update or migration. Verify function, proxied access and monitoring afterwards. The dated state above is not proof of current status.

## References
[Application directory](../README.md) · [Service inventory](../../Services.md) · [Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

Never publish credentials or live environment files.
