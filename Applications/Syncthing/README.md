# Syncthing

**Host(s):** Servius / TrueNAS SCALE; h711nt / Windows 11  
**Observed deployment:** Servius TrueNAS-managed `ix-syncthing-syncthing-1`, image `syncthing/syncthing:2.1.6` Oct 14; h711nt Windows receiver. Original `speedpool-appdata` folder sends from Servius and receives on h711nt.

## What it does
Replicates file sets and selected backup archives between hosts.

## Dependencies and data
Observed appdata replication indexing and transient failures involving SQLite WAL/SHM, Authentik PostgreSQL, Fail2ban DB, PID files. Carefully selected source ignore rules were applied.

## Practical operations
Never revert local changes or reindex a full folder to resolve a cosmetic zero-byte `arr_common` warning. Syncthing replication is not an app-consistent database backup. Confirm send-only/receive-only directions and independent restore tests. See [appdata audit](2026-10-15-Appdata-Audit.md).

## Safe maintenance
Verify live container identity, mounts, network, pool health and recoverable backup before any change. Use the owning manager (TrueNAS Apps or Dockhand) and avoid duplicate deployments. Preserve data and confirm intended user-facing behavior after updates.

## More information
[Application index](../README.md) · [Host/service inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

_Evidence is dated Oct 2026, not a live status check. Never put secrets or complete .env files in GitHub._
