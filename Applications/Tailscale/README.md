# Tailscale

**Host(s):** Servius / TrueNAS SCALE; HomePi / DietPi  
**Observed deployment:** Servius: TrueNAS-managed `ix-tailscale-tailscale-1` (`ghcr.io/tailscale/tailscale:v1.102.5` observed Oct 14). HomePi: network Tailscale instance configured to advertise residential exit and LAN subnet routes.

## What it does
Encrypted overlay network for secure remote management and user-selected exit nodes.

## Dependencies and data
HomePi advertises `0.0.0.0/0`, `::/0` and `192.168.50.0/24`; IPv4/IPv6 forwarding and masquerading were verified. Servius existing Tailscale also advertised subnet/exit routes, but IPv6 forwarding was disabled in last check.

## Practical operations
Do not modify Servius default route or existing trusted management identity while experimenting. A separate AirVPN exit identity is planned, not deployed. Check approved routes and exit egress with client before claiming VPN protection.

## Safe maintenance
Verify live container identity, mounts, network, pool health and recoverable backup before any change. Use the owning manager (TrueNAS Apps or Dockhand) and avoid duplicate deployments. Preserve data and confirm intended user-facing behavior after updates.

## More information
[Application index](../README.md) · [Host/service inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

_Evidence is dated Oct 2026, not a live status check. Never put secrets or complete .env files in GitHub._
