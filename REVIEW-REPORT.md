# Audit and handoff notes

**Reviewed:** October 2026 repository export. **Verification is not equivalent to a live Servius inspection.**

## Changes made

- Replaced the root README with a clear, short navigation page.
- Added emergency triage, application index, backup checklist, incident/application templates, and edit guide.
- Consolidated duplicate hardware inventory into `Infrastructure/Hardware/`.
- Preserved existing Compose/YAML content and dated Docker snapshot without automatically changing deployments.
- Added links to official TrueNAS, ZFS and Docker documentation rather than copying uncertain instructions.

## Verification levels

- **Repository-verified:** internal links and preserved configuration files; file references exist in this package.
- **Source-derived (dated):** Docker container statuses, image versions, hardware layout, backup paths and activities recorded October 14, 2026.
- **Official guidance:** TrueNAS UI/API operations are version-dependent; use current release documentation before acting.
- **Not independently verified:** actual current pool health, operating system version, restore success, cloud backup status, Compose management ownership and live DNS/proxy settings.

## Priority checks before relying on procedures

1. Confirm `praesidium` health, missing disk(s), cable status and newest independent backups.
2. Confirm backup configuration export **with secret seed** exists outside this repository.
3. Confirm single-NVMe `speedpool` appdata and VM have restorable copies; live Syncthing is not automatically a consistent database backup.
4. Test-restoring each AMP game in a separate temporary environment.
5. Check the unhealthy Kuma Container Sync issue and cloud backup-task failures.
6. Confirm Kioxia boot mirror remains **not configured** until physically installed and validated.

## Privacy and published config

Automated pattern matching is insufficient to certify that a file has no secrets. Human review is still required, especially for `.env.example`, Homepage YAML, Compose YAML, screenshots, and Git history. The old repository had historically contained secrets: **rotate any still-active exposed credentials**; deletion alone cannot revoke them.

## Editing

Use [CONTRIBUTING.md](CONTRIBUTING.md). For new issues, copy [Templates/Incident.md](Templates/Incident.md), fill in actual commands, then link it from [Troubleshooting/README.md](Troubleshooting/README.md).
