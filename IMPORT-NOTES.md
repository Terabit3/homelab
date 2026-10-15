# Legacy migration notes

Included:
- `Infrastructure/Servius.md`: consolidated old hardware and Servius overview; replaces stale service list with pointer to current Docker snapshot.
- `Infrastructure/homepi.md`: legacy Raspberry Pi facts and a checklist of settings to verify.
- `Applications/Pi-hole/DNS-Editing.md`: preserved the goal of the batch-editing procedure without assuming old `custom.list` paths still work.
- `Applications/Nginx Proxy Manager/Pi-hole-Proxy-Redirect.md`: preserved an older Lighttpd fix as historical only.

Not imported:
- Empty Nextcloud README template.
- Old undated services inventory as a source of current service status.
- Blank Operations Guide templates.

Review each file before committing. The bundle does not include Compose YAML, `.env` values, passwords, or keys.
