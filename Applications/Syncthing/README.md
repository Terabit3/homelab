# Syncthing

## Hosts
**Servius (TrueNAS SCALE) and h711nt (Windows 11).** Servius: send-only appdata source. h711nt: receive-only replica for configured folders.

## Purpose
Peer-to-peer folder replication between hosts.

## Homelab notes
A live replicated SQLite or PostgreSQL database is not an application-consistent restore. Keep Send Only / Receive Only roles explicit.

**Evidence status:** Dated observations through 2026-10-16; current container state must be checked on each host.

## Related
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations](../../Operations/README.md)

Do not publish login credentials or environment files.
