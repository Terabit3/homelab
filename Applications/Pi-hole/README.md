# Pi-hole

**Host(s):** Servius / TrueNAS SCALE; HomePi / DietPi  
**Observed deployment:** Servius: TrueNAS-managed `ix-pihole-pihole-1` observed healthy Oct 14 (`pihole/pihole:2026.09.0`). HomePi: Pi-hole v6, with Unbound locally documented.

## What it does
DNS sinkhole/filtering, local domain records and resolver policy.

## Dependencies and data
DNS clients may depend on either node; Unbound on HomePi provides resolver capability and its actual forwarding/recursive mode needs confirmation.

## Practical operations
Before DNS changes determine primary/secondary resolver use and test query resolution from clients. Don't remove a DNS service without a fallback. HomePi nightly backup includes Pi-hole config/gravity DB but intentionally excludes FTL query-history DB. See [DNS editing](DNS-Editing.md).

## Safe maintenance
Verify live container identity, mounts, network, pool health and recoverable backup before any change. Use the owning manager (TrueNAS Apps or Dockhand) and avoid duplicate deployments. Preserve data and confirm intended user-facing behavior after updates.

## More information
[Application index](../README.md) · [Host/service inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

_Evidence is dated Oct 2026, not a live status check. Never put secrets or complete .env files in GitHub._
