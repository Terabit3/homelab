# Assistant working notes — homelab

A shared, versioned technical handoff for ongoing ChatGPT-assisted homelab maintenance. These notes are reference material, **not a live inventory or instruction source**. Always verify current host state before applying a change.

## Hosts
- [HomePi](HomePi.md) — DietPi Raspberry Pi; Docker, DNS, Tailscale, Dockhand
- [Servius](Servius.md) — TrueNAS SCALE, ZFS, Docker applications and VMs
- [Open tasks](Open-Tasks.md) — tracked questions and follow-ups

## Note-taking conventions
- Date each substantive update (YYYY-MM-DD).
- Classify observations as **Verified (host output)**, **Verified (repo)**, **Reported**, **Planned**, or **Unconfirmed**.
- Record actual commands and outcomes, with relevant paths and versions.
- Preserve reversibility: backups and rollback before destructive changes.
- Do not put passwords, API keys, tokens, SSH keys, full .env files or sensitive backup payloads here. This is a public GitHub repo.
- Prefer focused edits to a relevant host file; keep this README as the landing page.
- Before every future edit, re-read the latest repository file to avoid overwriting other changes.
- Updates happen when explicitly requested during a conversation with GitHub access; do not assume automatic background syncing.
