# Servius ↔ h711nt: dedicated 10GbE DAC link

> **Status: PLANNED / NOT INSTALLED OR TESTED.** Updated 2026-10-16. This is an intended point-to-point transfer link, not the existing LAN. Do not assume 10GbE is operational until independently verified.

## Purpose and topology

Connect **Servius (TrueNAS SCALE)** directly to **h711nt (Windows 11)** for bulk file transfer (including approximately 4 TB of planned personal data). Retain existing LAN connections on both machines for internet, management, DNS, and normal services. No 10Gb switch is required for this direct connection.

```text
Existing LAN/router --- 1GbE --- Servius (TrueNAS)
                  \--- 1GbE --- h711nt (Windows)

Servius 10Gb SFP+ <-- 3 m passive SFP+ DAC --> h711nt 10Gb SFP+
```

## Hardware identified from photos (2026-10-16)

| Host / role | Network card | Details |
|---|---|---|
| Servius (planned) | Dell OEM Broadcom dual-port SFP+ | Broadcom BCM57810S family; PCB marking BCM957810A1006G; Dell label appears **0N20KJ** (confirm via `lspci -nn` and Dell sticker before ordering vendor-coded DAC) |
| h711nt (planned) | Single-port Intel 82599ES SFP+ | GigaPlus-branded / Intel 82599ES-based card per supplied label; verify hardware ID, driver and cable acceptance in Windows |
| Link medium (to purchase) | **10Gb SFP+ passive direct-attach copper (DAC)** | **3 metres**, twinax, ideally 24 AWG; no separate transceivers required |

**Placement:** The two upright PCs are situated beneath the desk with ports facing away from each other. A 3 m cable provides routing slack; measure actual route around cases and check strain relief and the cable manufacturer's minimum bend radius before purchase.

**Procurement:** Prefer a 3 m dual-coded cable with **Intel 82599-compatible** coding at the workstation end and **Dell/Broadcom 57810-compatible** coding at the server end, validated by the seller for both exact adapters. 10Gtek is a budget candidate; FS.com custom dual-coded DAC is an alternative. Generic/Cisco-coded or Dell-only-coded listings are *not* proof of interoperability. Confirm shipping, returns, and both firmware/driver compatibility before ordering. This cable is **not yet purchased**.

## Physical installation and thermal safety

- Power down both machines, install each card in a suitable PCIe slot, and confirm proper clearance/airflow.
- **Broadcom card has a small onboard heatsink fan reported to be loud.** Do not simply disable it and assume case airflow is sufficient. Test temperatures during sustained line-rate transfer with the stock fan first; investigate an appropriately rated quiet replacement or directed case-fan duct, confirming pinout and voltage before connecting a different fan.
- Passive-cooled server NICs still need forced airflow; the Define 7 airflow at the PCIe slot must be checked.
- Route DAC away from sharp edges and high-heat components; avoid tight bends and cable strain at SFP+ sockets.

## Configuration plan — only after installation

1. **Servius:** Identify adapter and interface names using `lspci -nn`, `ip -br link`, and TrueNAS network UI; inspect driver/messages with `dmesg`. Use supported TrueNAS UI/API configuration, not transient Linux-only settings, for persistent network setup.
2. **h711nt:** Check Device Manager hardware IDs, driver health, adapter status and negotiated link speed; verify Intel 82599-based card is usable on its installed Windows release.
3. Assign a **separate, non-overlapping point-to-point subnet**, for example `10.77.10.1/30` to Servius and `10.77.10.2/30` to h711nt, **only if this range is unused**. No gateway or DNS on the dedicated adapters; leave the existing default gateway on the primary LAN.
4. Confirm the cable negotiates **10,000 Mb/s full duplex** on both ends, then test both directions with `iperf3` (where installed) and sustained SMB file transfers.
5. If using jumbo frames, enable them on **both endpoints only after baseline tests**; 1500-byte MTU is the safe starting point.
6. Check CPU, NIC temperatures, errors/drops, and achieved throughput during prolonged file transfers. Do not mistake file/disk throughput for wire-rate throughput.
7. For SMB transfer, connect to Servius's dedicated 10Gb address explicitly, so Windows does not silently take the 1Gb LAN path. Verify SMB access policy/firewall and avoid opening shares to untrusted networks.
8. Record actual interface names, firmware, driver, operating temperatures, throughput, link addresses and tested recovery behavior here after successful deployment.

## Recovery and troubleshooting

- **No link:** Check both adapters recognized; DAC seated and latched; driver/firmware; vendor cable coding; alternate ports; link messages. Generic DACs may be rejected on either side.
- **Slow transfer:** Confirm source/destination IP path, 10Gb negotiation, SMB behavior, disk bottlenecks, CPU limits and network errors; use `iperf3` to isolate network from storage performance.
- **NIC overheating or fan failure:** Stop sustained transfers and restore cooling; do not treat a warm heatsink touch-test as sufficient.
- **Loss of management connectivity:** Preserve existing 1Gb management/LAN configurations and their default routes. Revert only the dedicated link's networking changes.
- **Documentation during rebuild:** The 10Gb link is optional. Restore 1Gb LAN access and data/backup integrity before configuring this optimization.

## Pending checklist

- [ ] Confirm precise NIC PCI IDs and vendor-specific DAC support at both ends.
- [ ] Measure final cable path and buy suitable **3 m passive 10Gb SFP+ DAC**.
- [ ] Verify safe Broadcom cooling strategy under actual airflow and load.
- [ ] Install cards, verify Windows/TrueNAS drivers and 10Gb link.
- [ ] Configure independent point-to-point IPs without altering LAN defaults.
- [ ] Benchmark `iperf3`, SMB, sustained transfer and temperature.
- [ ] Update hardware inventory, topology and this runbook with verified state.
