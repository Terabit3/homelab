# Pi-hole — Editing Local DNS Records

> **Legacy note migrated for reference.** The original guide edited `custom.list` directly. Paths and reload methods vary by Pi-hole version and installation. Prefer the supported Pi-hole admin interface/API for routine changes.

## What the old procedure did

The old documentation edited `custom.list` to add multiple host-to-IP mappings:

- TrueNAS installation (historical path): `/mnt/speedpool/appdata/pihole/config/custom.list`
- Bare-metal installation (historical path): `/etc/pihole/custom.list`

These paths **must be verified** against the current Pi-hole app's persistent mounts. The current TrueNAS Pi-hole image was recorded as `pihole/pihole:2026.09.0`; older file layouts may not apply.

## Current approach

1. Open the active Pi-hole administrative interface.
2. Find the **Local DNS** records section (wording may differ by version).
3. Add or edit records and check that clients resolve them correctly.
4. For large batches, review the version-specific supported interface or API before automating.

## Verification (from a client)

```bash
nslookup example.internal <PIHOLE_IP>
```

Replace the example domain and address with the actual record and DNS server. Run after allowing for client caching.

## Historical maintenance command

The old guide used `systemctl restart pihole-FTL`, which is relevant only to installations managed by systemd. **Do not run that command on the TrueNAS host expecting it to restart a container.** For a containerized installation, inspect the correct container logs and restart mechanism only if required.

## To verify

- Persistent configuration mount for the current Pi-hole container.
- Supported local-DNS management mechanism for the installed Pi-hole version.
- Which DNS changes require reload and which are applied immediately.
