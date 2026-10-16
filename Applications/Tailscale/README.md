# Tailscale

## Hosts
**Servius (TrueNAS SCALE) and HomePi (DietPi).** HomePi residential exit/subnet router; Servius existing Tailscale app. Separate AirVPN exit identity planned only.

## Purpose
Encrypted private mesh network, subnet routing and optional exit nodes.

## Homelab notes
Do not change the TrueNAS host default route for experimental VPN gateways. Verify client-selected exit nodes before relying on them.

**Evidence status:** Dated observations through 2026-10-16; current container state must be checked on each host.

## Related
- [Application index](../README.md)
- [Current Servius handoff](../../Assistant-Notes/Servius.md)
- [Service inventory](../../Services.md)
- [Operations](../../Operations/README.md)

Do not publish login credentials or environment files.
