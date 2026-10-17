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

### Planned 10Gb networking upgrade (not installed)

- **Servius:** Dell OEM Broadcom BCM57810S-family dual-port SFP+ adapter, photographed October 16, 2026; PCB marking `BCM957810A1006G`; Dell part label appears `0N20KJ` (confirm hardware ID).
- **h711nt:** Intel 82599ES-based single-port SFP+ adapter, GigaPlus branding.
- **Interconnect:** planned 3 m passive 10Gb SFP+ DAC, ideally vendor-coded Intel on one end and Dell/Broadcom on the other; **not yet bought or tested**.
- **Cooling caution:** Broadcom card's onboard fan is loud. Do not disable without verifying adequate forced heatsink airflow and loaded temperature.
- Details, procurement checks, setup, troubleshooting and recoverability: [Servius ↔ h711nt 10GbE plan](../Networking/Servius-h711nt-10GbE.md).

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

### Pending upgrade: Kioxia boot mirror

- Current TrueNAS boot drive: Samsung 870 EVO 500 GB SSD.
- Second boot drive: Kioxia SSD (model/capacity to verify).
- Status: Kioxia is physically disconnected due to the outstanding power-cable issue.
- Plan: Connect Kioxia and configure it as a mirror of the existing TrueNAS boot pool.
- Current boot pool has no confirmed redundancy.

Before installation:
1. Resolve the power-cable problem.
2. Back up the TrueNAS configuration, including the secret seed.
3. Identify the Kioxia SSD by serial number.
4. Use the TrueNAS boot-pool attach procedure; do not create a new pool.
5. Wait for resilvering and verify boot-pool health.

WARNING: Attaching the Kioxia SSD will overwrite its existing contents.

See [disk and pool procedures](../../Operations/TrueNAS-Disk-Replacement-and-Expansion.md) for the safety checklist and version-specific boot-pool instructions.
