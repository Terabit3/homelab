# Homelab Hardware Inventory

> Historical inventory copied from earlier homelab notes. Verify components and roles against the current systems before treating this as a current snapshot.

## Servius — TrueNAS SCALE

| Component | Hardware / configuration |
|---|---|
| CPU | Intel Xeon E-2246G |
| CPU cooler | Noctua NH-D15 |
| Motherboard | Supermicro X11SCA-F (ATX, LGA1151) |
| RAM | Kingston KSM26ED8/32ME, 2 × 32 GB DDR4-2666 ECC CL19 (64 GB total) |
| Boot drive | Samsung 870 EVO 500 GB SATA SSD |
| Applications / fast storage | Samsung 990 PRO with heatsink, 2 TB NVMe; `speedpool` |
| Bulk storage | 4 × HGST Ultrastar He12 12 TB, model HUH721212ALE601; `praesidium` |
| Case | Fractal Design Define 7 |
| PSU | ASUS ROG Strix 650 W, 80+ Gold, fully modular |

### Useful storage paths

- `speedpool` app data: `/mnt/speedpool/appdata`
- `praesidium` data: `/mnt/praesidium`

These paths describe the documented layout, not a live validation of mounts or dataset properties.

## homepi

| Component | Hardware / configuration |
|---|---|
| Device | Raspberry Pi 3-series board (previous note says “3+”; confirm exact model) |
| Operating system | DietPi (historical; verify current version) |
| Original purpose | Always-on DNS using Pi-hole / Unbound |

> **Verify current services:** homepi's role may have expanded, and Servius also runs a Pi-hole instance. Document the actual DNS arrangement before relying on this note during recovery.

## Hardware verification checklist

- [ ] Confirm installed CPU, RAM, storage devices and drive serials (keep serials in private inventory if preferred)
- [ ] Confirm TrueNAS pools, vdev layout and encryption/key recovery plan
- [ ] Confirm motherboard BIOS and BMC firmware versions
- [ ] Confirm homepi model, OS and active services
- [ ] Record any hardware replacements and test dates
