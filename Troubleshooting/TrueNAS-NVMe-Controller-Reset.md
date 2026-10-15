# TrueNAS — NVMe Controller Disconnects and `speedpool` Errors

## Symptoms recorded

TrueNAS notifications reported:

```text
Critical
Device: /dev/nvme0n1, failed to read NVMe SMART/Health Information.

Critical
Pool speedpool state is ONLINE: One or more devices has experienced an error resulting in data corruption. Applications may be affected.
```

The console showed an NVMe controller dropping offline:

```text
nvme nvme0: controller is down; will reset: CSTS=0xffffffff, PCI_STATUS=0xffff
nvme nvme0: Does your device have a faulty power saving mode enabled?
nvme nvme0: Try "nvme_core.default_ps_max_latency_us=0 pcie_aspm=off" and report a bug
```

## Suspected cause

NVMe / PCIe power management may have contributed to controller resets. The original troubleshooting notes documented disabling NVMe autonomous power-state transitions and PCIe ASPM as a workaround.

**This was a proposed fix in the legacy notes, not independently verified here as the confirmed root cause or a currently required Servius setting.** NVMe dropouts can also result from firmware, PCIe slot, power, thermals, or hardware failure.

## Before making changes

1. Ensure application data and important files have a verified backup.
2. Check actual disk and ZFS health; an `ONLINE` pool can still report data errors.
3. Note the current kernel settings and TrueNAS version.
4. Treat this as a maintenance-window change because testing may require rebooting.

Useful read-only checks on Servius:

```bash
sudo zpool status -v speedpool
sudo dmesg -T | grep -iE 'nvme|pcie|aer'
cat /proc/cmdline
```

## Workaround documented in the original notes

The original recorded command was:

```bash
midclt call system.advanced.update '{"kernel_extra_options": "nvme_core.default_ps_max_latency_us=0 pcie_aspm=off"}'
```

**Do not run this command unchanged without confirming your installed TrueNAS SCALE version still supports the field and checking existing kernel options.** Updating `kernel_extra_options` may replace other boot parameters. The options disable some NVMe and PCIe power-saving behaviour and can increase power consumption. The appropriate method for applying kernel parameters may differ between TrueNAS versions.

The documented procedure called for a controlled reboot after applying the change.

## Verify after a maintenance-window reboot

```bash
cat /proc/cmdline
sudo dmesg -T | grep -iE 'nvme|pcie|aer'
sudo zpool status -v speedpool
```

Check that the parameters are present, that no new NVMe controller resets occur, and that the pool no longer accumulates errors. Monitor over time; a quiet log immediately after reboot is not proof of a permanent fix.

## If the problem returns

- Check NVMe firmware and device health.
- Check SSD temperature, PCIe slot/adapter seating, and motherboard BIOS settings.
- Determine whether ZFS recorded permanent errors and whether affected files need restoration.
- Reassess the power-management workaround rather than simply ignoring warnings.

## Reference

[TrueNAS community: NVMe crashing system](https://www.truenas.com/community/threads/nvme-crashing-system.114832/)

*Preserved and revised from legacy homelab notes. The exact fix outcome and current Servius kernel settings have not been independently confirmed.*
