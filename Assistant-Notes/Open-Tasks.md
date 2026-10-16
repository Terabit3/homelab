# Open tasks (summary)

Use **[Servius handoff](Servius.md)** for the up-to-date ARR migration status and **[ROADMAP](../ROADMAP.md)** for longer-term plans.

## Urgent — Servius
- [ ] Diagnose and restore `praesidium` RAIDZ2 member (currently DEGRADED, one UNAVAIL); verify independent recovery.
- [ ] Verify speedpool appdata backups before migration.
- [ ] Confirm Cleanuparr destructive cleanup options and protect private tracker seeding.

## ARR Compose restructuring (prepared, not deployed)
- [x] Agree four main downtime-priority stacks plus independent optional apps.
- [x] Stage eight Compose projects and validate all 8/8.
- [x] Confirm Dockhand can read staging Compose and .env.
- [ ] Backup and map persisted data (especially Gluetun/qBit, Jellystat Postgres and Cleanuparr).
- [ ] Validate staged vs currently running containers, networks and environment.
- [ ] Migrate low-criticality stacks individually and verify health; downloads last.
- [ ] Reimport/update Dockhand stacks; archive obsolete Compose projects only after verification.
- [ ] Fix standalone Whisparr ARR network integration during controlled cutover.

## HomePi
- [ ] Confirm existing Compose directory, mounts, and Dockhand access.
- [ ] Check Uptime Kuma migration/monitor coverage and HomePi backup restoration.
- [ ] Validate backup, authentication and Tailscale residential exit from a client.

_Status checked against host output Oct 16, 2026; never assume checked boxes imply current service health._
