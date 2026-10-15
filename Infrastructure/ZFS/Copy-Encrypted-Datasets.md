# TrueNAS — Copy an Encrypted ZFS Dataset to Another Pool

## What this fixes

Move or replicate an encrypted dataset between ZFS pools while keeping its snapshots and encryption intact. This guide preserves the example of moving `speedpool/appdata` to `praesidium/appdata`.

> **Before running:** These are example names from an earlier migration, **not a change to make on Servius now**. Check the live dataset layout, free space, existing snapshots, and backup state first. Do not overwrite an existing destination dataset.

## Important encryption detail

`zfs send -w` sends **raw encrypted data**. It **preserves** the source dataset's encryption and key requirements; it does *not* remove encryption or automatically re-encrypt the received dataset with `praesidium`'s key. Keep the source dataset's recovery key/passphrase available. A raw send can generally be made without unlocking the source encrypted dataset, provided the snapshot is accessible.

If the goal is to **change encryption keys** on the destination, raw replication is not that procedure. Plan a separate receive strategy and confirm the TrueNAS encryption and restore requirements first.

## 1. Inspect the source and destination (read-only)

Run on the TrueNAS host:

```bash
sudo zfs list -o name,used,avail,encryption,keystatus,mountpoint speedpool/appdata praesidium
sudo zfs get encryption,encryptionroot,keystatus speedpool/appdata
sudo zfs list -t snapshot -r speedpool/appdata
```

Confirm that the destination name is available, that the target pool has sufficient space, and that you have backed up the encryption key separately. If `praesidium/appdata` already exists, **stop**: the example below is for a new destination, not for overwriting it.

## 2. Take a recursive snapshot

```bash
sudo zfs snapshot -r speedpool/appdata@migrate
```

This makes a consistent point-in-time reference for each dataset in the hierarchy. For actively changing applications or databases, consider stopping writes or taking an application-consistent backup first.

## 3. Preview the receive

```bash
sudo zfs send -R -w speedpool/appdata@migrate | sudo zfs receive -nuv praesidium/appdata
```

The `-n` flag makes this a **dry run of the receive**. Inspect the resulting messages. The source snapshot must exist, and a raw receive must respect the destination's encryption and hierarchy constraints.

## 4. Replicate to a new destination

**Only after checking the dry run and ensuring the destination does not already exist:**

```bash
sudo zfs send -R -w speedpool/appdata@migrate | sudo zfs receive -u praesidium/appdata
```

- `send -R`: sends a replication stream including descendant datasets and their snapshots.
- `send -w`: sends the encrypted dataset as raw data, preserving source encryption.
- `receive -u`: does not automatically mount the received datasets.

This command may be inappropriate for mixed encrypted/unencrypted dataset trees or existing target hierarchies; verify those details beforehand. Do not use `zfs receive -F` casually: it can roll back or destroy destination data.

## 5. Verify the result

```bash
sudo zfs list -r -o name,used,refer,encryption,keystatus,mountpoint praesidium/appdata
sudo zfs list -t snapshot -r praesidium/appdata
sudo zfs get encryption,encryptionroot,keystatus praesidium/appdata
```

Confirm that expected descendant datasets and snapshots exist, and inspect the key status. For actual recovery confidence, unlock/mount the new dataset using the **appropriate source encryption credentials** and verify file contents and application data. A successful `zfs list` alone does not prove every file or application is usable.

## 6. Cut over applications carefully

Only after verification, update application mount paths if needed, confirm ownership and ACLs, and test services. Keep the original dataset until the migration is validated and you have a working recovery plan.

## Troubleshooting reminders

- **Destination already exists:** Stop; decide whether you need incremental replication, a different destination name, or another migration method.
- **Cannot unlock the received dataset:** Raw replication keeps the source encryption relationship. Check the source key/passphrase and the imported dataset's encryption root.
- **Insufficient space:** Check destination pool capacity, snapshots and dataset reservations.
- **Application starts but cannot access files:** Check mount paths, dataset mount status, ACLs, and permissions.

## What I learned

- Take a snapshot before copying a dataset.
- Raw send (`-w`) **keeps encryption**, rather than stripping or converting it.
- Check the destination and test with `zfs receive -n` before doing the real transfer.
- Document which encryption key is needed to unlock the destination.
- Confirm application functionality before removing the old data.

## Source

Adapted from the previous homelab guide, **“Copy Encrypted Datasets to a Different Pool.”** Commands have been clarified for safe future reuse. The example does not establish that the migration was actually performed successfully.
