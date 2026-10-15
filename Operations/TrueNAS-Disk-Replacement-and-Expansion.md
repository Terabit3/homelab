# TrueNAS SCALE — Replacing Disks and Adding Capacity

> **For Servius:** `praesidium` is historically recorded as **4 × 12 TB RAIDZ2**; `speedpool` is a **single NVMe device**. Confirm current topology with `sudo zpool status -v` before every storage change. These steps reference the TrueNAS SCALE 24.10 GUI. **Nothing in this guide should be executed without identifying the actual disk and checking backup health.**

## Safety rules before touching disks

1. Verify the **pool** and exact **vdev topology**. Never infer RAID level from the number of physical drives.
2. Save a current **TrueNAS configuration backup with secret seed** to the private vault and confirm an independent data backup.
3. Match **serial number**, **model**, and **bay/cable**. `/dev/sda` or `/dev/sdb` names are not stable physical identifiers.
4. Check the new disk is not in a pool and meets capacity requirements. **TrueNAS replaces/wipes the selected new disk.** Never select a disk holding data you want to retain.
5. Identify whether hardware is hot-swappable. If not, **shut down** before disconnecting SATA/data/power connectors.
6. Do not remove another disk or initiate another replacement while resilvering. Record initial and final status.

### Read-only identification commands

```bash
sudo zpool status -v
sudo zpool list -v
lsblk -o NAME,SIZE,MODEL,SERIAL,TYPE,MOUNTPOINTS
ls -l /dev/disk/by-id/ | head -n 60
```

Use the TrueNAS **Storage → pool → Manage Devices** view to confirm member disk serials. Label the physical bay/cable before unplugging anything.

## A. Replace a FAILED disk in `praesidium`

1. Confirm whether a disk is actually failing, missing because of a **power/data cable**, or disabled. Check `zpool status`, SMART, and the TrueNAS alert. If `praesidium` has a missing power connection, correct the cable issue **before assuming the drive is defective**.
2. Identify the old disk by **serial number** and its matching RAIDZ2 member. Check how many other disks are healthy and whether an existing resilver is running.
3. If possible, connect the replacement in a spare bay first. A replacement needs capacity **at least as large** as the device being replaced (nominally equal disks may differ slightly in usable bytes).
4. TrueNAS **Storage → select `praesidium` → Manage Devices → select the failed member → Replace**. Select the **verified new disk**.
5. If the disk must be swapped in the same bay, follow the TrueNAS **Offline → physical swap → Replace** procedure **only after confirming sufficient redundancy**. Shut down first for non-hot-swap systems.
6. Confirm the replacement. TrueNAS will erase the **replacement** and start resilvering. `Force` on a target with partitions/data is destructive: only use it if you have checked the target's serial and know its contents are disposable.
7. Watch progress with `sudo zpool status praesidium`. Wait for resilver completion. Do not do additional disk changes while it runs.
8. Verify `ONLINE`, no new error growth, TrueNAS alerts cleared where appropriate, shares accessible and apps reading expected data. Run/confirm an appropriate scrub after stabilization and check SMART on remaining disks.
9. Document **old serial → new serial**, failure symptom, bay, resilver duration, and last known-good backup.

**If a disk goes missing during resilver, STOP and reassess remaining redundancy.** If the pool is `UNAVAIL` or permanent errors appear, avoid force operations; preserve evidence and consider specialist recovery assistance before writing more data.

## B. Replace a failing-but-still-present disk

Prefer installing the new disk in a separate bay and selecting **Replace** while the old disk remains accessible, if supported by hardware and disk condition. This can preserve more redundancy during resilver. TrueNAS may recommend offlining a severely failed disk before replacing it. Follow the actual device's health and official version-specific replacement workflow; a replacement does **not** guarantee recovery of data already corrupted beyond parity capability.

## C. Replace the single NVMe in `speedpool`

**Critical:** `speedpool` has no redundancy. Replacing its sole member is **not** the same as swapping one RAIDZ2 disk. You cannot rely on resilvering from another pool member.

1. Before failure: maintain a **verified independent** appdata/database backup and any VM data needed to rebuild. Syncthing alone may copy inconsistent live databases; use application/database-aware backups where needed.
2. If NVMe is readable: arrange a supported migration/replication to new storage, verify recoverability, then cut services over in a planned window.
3. If NVMe is dead: replace the drive, create an appropriate new pool/dataset layout in TrueNAS, restore backup data/configs, fix mounts/permissions, and redeploy apps/VMs from verified definitions.
4. Restore databases using correct application procedures and verify at least one end-to-end app workflow.

**Do not run `zpool replace` assuming it will reconstruct a single-disk pool from nowhere.**

## D. Add MORE space to `praesidium` — choose the right method

There are **three different operations**. They are not interchangeable.

| Goal | Method | Important consequence |
|---|---|---|
| Add one more 12 TB-or-larger disk to existing RAIDZ2 group | **RAIDZ vdev expansion** (supported in SCALE 24.10+; check installed version and UI) | Changes width of existing RAIDZ2 vdev; existing parity level remains RAIDZ2. Requires a healthy pool, time and capacity planning. |
| Add a second redundant group of disks | **Add a new data VDEV** with appropriate matching layout | ZFS stripes pool data across vdevs. **Loss of any top-level data vdev can lose the whole pool**, even if other vdevs are healthy. Never add a single-disk stripe as a casual expansion. |
| Replace all 12 TB members with bigger disks | **One-by-one larger disk replacement**, then Expand | Replace/resilver **one disk at a time**; usable vdev capacity increases only when all relevant members support the larger size and expansion is completed. |

### Option 1: Extend existing RAIDZ2 with one extra disk

1. Confirm TrueNAS version supports **RAIDZ expansion** and `praesidium` is healthy (`ONLINE`; no active resilver/critical alerts).
2. Verify a separate backup and free capacity/headroom. Expansion is a substantial workload; do not use it to mask an existing disk/cable problem.
3. Identify and install a compatible new disk (same size or larger, in an empty bay). Confirm its serial.
4. In **Storage → `praesidium` → Manage Devices**, look for the current release's **Extend/Expand RAIDZ** workflow (UI wording varies). **Review** the preview before proceeding; do not use **Add VDEV → Stripe** as a substitute.
5. During expansion, monitor `sudo zpool status praesidium` and avoid other major storage operations.
6. After completion, check capacity, topology, errors and backups. RAIDZ expansion changes how new data is written; older blocks do not automatically get rewritten into the new width, so reported capacity efficiency may differ from a newly created wider vdev.

### Option 2: Add another RAIDZ2 vdev

Only use when you have enough compatible disks for a correctly redundant new vdev. Review total pool risk: when a pool has several top-level data vdevs, **all must survive**. Use TrueNAS's **Add VDEV** wizard, confirm disk selection/layout and verify backups first. This is different from adding one disk to the existing RAIDZ2 vdev.

### Option 3: Increase size by replacing every RAIDZ2 disk

1. Replace each member with a larger disk using the verified serial and the **Replace** workflow.
2. **Wait for a complete resilver before replacing the next member.**
3. Once all members are larger and healthy, use **Storage → Expand** if needed to claim the additional space.
4. Confirm final capacity and pool health.

## E. Add a separate new storage pool

Choose this when you want another independent failure domain rather than growing `praesidium`/`speedpool`.

1. Plan number of disks, fault tolerance, usable capacity and datasets.
2. Back up any existing content on target disks; pool creation erases them.
3. Install disks, identify serials, open **Storage → Create Pool**, select the correct vdev layout (mirror, RAIDZ, etc.), review and create.
4. Set up datasets, ACLs, snapshot/replication tasks and shares before moving apps.
5. Validate backups and monitoring for the **new pool separately**.

## F. After replacement or expansion — verification checklist

```bash
sudo zpool status -v
sudo zpool list -v
sudo zfs list -o name,used,avail,mountpoint
```

- Pool is `ONLINE` with no in-progress replace/expand/resilver operation.
- Disk model/serial mapping reflects intended hardware.
- No new read/write/checksum errors are accumulating.
- SMB/NFS shares, Docker mounts, databases, Syncthing and AMP VM storage resolve correctly.
- Snapshot and backup tasks run for the intended datasets.
- Documentation inventory and hardware notes updated.

## Stop and ask for help if

- The disk serial/bay cannot be identified positively.
- `praesidium` has **multiple missing/unreadable disks** or ZFS shows permanent data errors.
- `speedpool` is intermittent and the only good appdata copy might be on that NVMe.
- The TrueNAS UI shows a proposed **Stripe**, **Force**, **Delete**, **Export/Disconnect**, or **Wipe** action that does not match your intent.
- You cannot confirm at least one usable, independent backup for irreplaceable data.

## Sources (TrueNAS 24.10 unless otherwise noted)

- [Managing Pools — expanding a RAIDZ vdev, adding vdevs, replacing disks](https://www.truenas.com/docs/scale/24.10/scaletutorials/storage/managepoolsscale/)
- [Replacing Disks](https://www.truenas.com/docs/scale/24.10/scaletutorials/storage/disks/replacingdisks/)
- [Create Pool](https://www.truenas.com/docs/scale/24.10/scaletutorials/storage/createpoolwizard/)
- [RAIDZ Extension Calculator](https://www.truenas.com/docs/references/extensioncalculator/)
