# Homelab Hardware Testing Guide

> Reference guide for testing hardware **before deployment or after suspected faults**. Do not launch stress or disk tests on production Servius without planning downtime and verifying backups. Commands are examples, not recorded proof that a test was performed.

## 1. Test RAM with MemTest86

**Purpose:** Find memory errors that can cause unexplained crashes, data corruption or installation failures.

1. Download the official bootable USB package from [MemTest86](https://www.memtest86.com/).
2. On a separate PC, create the bootable USB following the package's instructions. **Do not assume a downloaded file is an ISO**; the official package may include its own USB-writing utility/image.
3. Boot the target system from that USB using the motherboard boot menu.
4. Run at least the default full test cycle; for intermittent problems, several passes or an overnight test are reasonable. Duration depends on memory capacity and platform.
5. Record the version, modules tested, pass count, temperatures if available, and any errors.

**Result:** Zero errors is expected. Any repeatable error merits investigation, but may involve RAM, memory slots, CPU memory controller, BIOS settings or overclocking—not necessarily a faulty module alone.

## 2. Stress-test the CPU with Prime95

**Correction to the old notes:** Prime95 is an application, **not a generic bootable USB image**. There is no standard `prime95-usb-image.iso` to flash with `dd`.

1. Download [Prime95](https://www.mersenne.org/download/) for a supported running operating system, or boot a Linux live USB and install/run the appropriate binary there.
2. Choose **Just Stress Testing**.
3. Use **Small FFTs** to emphasize CPU/thermal stability; larger or blended workloads also exercise memory and other components.
4. Monitor CPU temperature, clock behavior, system logs and test errors. Stop if cooling or power delivery is inadequate.
5. Record test duration, temperatures, load and outcome. Test duration should fit the suspected issue rather than relying on a fixed number of hours.

For a production TrueNAS appliance, do this during planned maintenance; don't install unrelated stress-test packages into the managed TrueNAS OS just for this guide.

## 3. Check drives with SMART self-tests

Identify the **correct physical device** first. Device names such as `/dev/sda` may change between boots; prefer stable `/dev/disk/by-id/` names when supported.

```bash
lsblk -o NAME,MODEL,SERIAL,SIZE,TYPE,MOUNTPOINTS
sudo smartctl -a /dev/sdX
```

Start one of the following **non-destructive** self-tests when supported by the drive:

```bash
sudo smartctl -t short /dev/sdX
sudo smartctl -t conveyance /dev/sdX   # Only if supported by the drive
sudo smartctl -t long /dev/sdX
```

The test runs in the drive's firmware and may take minutes to hours. After the estimated completion time:

```bash
sudo smartctl -a /dev/sdX
sudo smartctl -l selftest /dev/sdX
```

Check the self-test results and SMART attributes, including pending/reallocated sectors, uncorrectable errors, and interface errors. A SMART pass does not guarantee future reliability.

**TrueNAS note:** Prefer its built-in SMART test scheduling and reporting. Don't use `apt install smartmontools` on a managed TrueNAS SCALE installation; use available built-in tools or a separate testing OS.

## 4. Surface-test disks with `badblocks` and `tmux`

### Destructive warning

**`badblocks -w` is a destructive write-mode test. It overwrites the entire selected drive and destroys its data, partition table and pool membership. NEVER run it on an in-use TrueNAS pool or on any drive with data you want to keep.**

For established disks, begin with SMART tests and pool-level checks instead. Even `badblocks` read-only tests are heavy and can interfere with production workloads.

### For a new, empty disk only

Use a separate Linux testing environment, identify the drive by model, size and serial, and confirm twice that it is **not** part of an active pool or mounted filesystem.

```bash
lsblk -o NAME,MODEL,SERIAL,SIZE,TYPE,MOUNTPOINTS
sudo smartctl -a /dev/sdX
```

Start a detachable terminal session:

```bash
tmux new-session -s drive-test
```

`tmux` shortcuts:

- `Ctrl+B`, then `%`: split into side-by-side panes.
- `Ctrl+B`, then `"`: split into stacked panes.
- `Ctrl+B`, then arrow key: change panes.
- `Ctrl+B`, then `d`: detach without stopping commands.
- `tmux attach -t drive-test`: return later.

**Destructive example — never paste without first substituting and verifying the target drive:**

```bash
# ERASES ALL CONTENTS OF THE SELECTED DEVICE
sudo badblocks -wsv -b 4096 -o /root/badblocks-test.log /dev/sdX
```

The `-o` file records detected bad block numbers; it is not a complete progress or diagnostic log. For extensive testing, also retain the terminal output and SMART reports.

Large HDDs, especially 12 TB models, may require **days** for a full write-and-verify test. Good cooling, stable power and no interruptions are important.

## 5. Interpreting results and follow-up

| Test | Good result | Follow-up if it fails |
|---|---|---|
| MemTest86 | No memory errors | Retest with one module/slot at a time; examine BIOS/memory settings |
| Prime95 | No calculation errors, lockups or thermal problems | Check cooling, power, clocks and memory stability |
| SMART self-test | Completed without error | Review attributes, cables and drive replacement options |
| `badblocks` (unused disk) | No bad blocks detected | Do not deploy a disk that fails validation |

Record actual test dates and outcomes next to the hardware inventory. Testing cannot substitute for backups.

## References

- [MemTest86](https://www.memtest86.com/)
- [Prime95 / GIMPS](https://www.mersenne.org/download/)
- [FamilyBrown hardware validation notes](https://www.familybrown.org/dokuwiki/doku.php?id=fester:hvalid_hdd)
