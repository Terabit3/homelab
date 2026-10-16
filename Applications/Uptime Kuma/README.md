# Uptime Kuma

**Host(s):** Servius / TrueNAS SCALE; HomePi / DietPi  
**Observed deployment:** Servius: TrueNAS-managed `ix-uptime-kuma-uptime-kuma-1` version 2.5.6 observed, host port 31050. HomePi: `uptime-kuma` Docker container image `louislam/uptime-kuma:1`, port 3001, data bind `/home/dietpi/docker/uptimek`.

## What it does
Service health checks, monitor history, notifications and planned backup success heartbeats.

## Dependencies and data
Servius uses Docker monitor sync helper, Discord notification routes and daily backup monitoring. HomePi Uptime Kuma has been adopted in Dockhand without a reported redeploy; its monitoring scope remains separately verifyable.

## Practical operations
Verify notification delivery, monitor duplication, stopped-intentionally state and fresh backup push signals. Keep SQLite backups consistent and don't reset monitor history to fix failed checks. See [HomePi backup runbook](../../Operations/HomePi-Backup-and-Restore.md).

## Safe maintenance
Verify live container identity, mounts, network, pool health and recoverable backup before any change. Use the owning manager (TrueNAS Apps or Dockhand) and avoid duplicate deployments. Preserve data and confirm intended user-facing behavior after updates.

## More information
[Application index](../README.md) · [Host/service inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

_Evidence is dated Oct 2026, not a live status check. Never put secrets or complete .env files in GitHub._
