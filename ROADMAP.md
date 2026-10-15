# Homelab Roadmap / TODO
Updated: 2026-10-15

**Current priority:** Harden Servius and HomePi, eliminate operational faults, establish reliable automated backups, safe updates, alerting, and recovery. This is a planning checklist, not a live status report. Never commit credentials, .env contents, SSH keys, or Uptime Kuma Push tokens.

## October 15 operational follow-ups
- [x] Retire Parrot Docker deployment and all its project configuration; preserve WinApps and Whisparr.
- [x] Remove 340 unused historical Docker images after manifest preview, retain container images and one unused rollback candidate per repository; leave Docker volumes untouched.
- [x] Repair shell home and persistent baby-blue Bash prompt for TrueNAS user `timhu`.
- [x] Disable broken legacy `speedpool/ix-apps` snapshot cron; native ZFS snapshot tasks remain enabled.
- [x] Repair Kuma Docker host association and assign Discord notifications to Lidarr and Dockge; verify sync healthy.
- [ ] Delete stale Parrot Kuma monitor ID 63.
- [ ] Inspect 354 unreferenced Docker volumes **without pruning**; review Dockhand image-update policy.
- [ ] Run next manual Docker image/rollback cleanup review around **2027-01-13** (~90 days). No automatic pruning.
- [ ] Restore RAIDZ2 missing member after ordered SATA power cable arrives; verify pool and resilver, then scrub per safe maintenance plan.
- [ ] Restore offsite protection: MEGA cloud sync tasks currently disabled, one last state FAILED; speedpool replication task currently disabled.
- [ ] Revisit TrueNAS update 24.10.2.3 → 24.10.2.4 after config backup/health verification.
- [ ] Migrate `timhu` SSH from password override to tested public-key-only access via TrueNAS supported settings.

## P0: Storage and recoverability
- [ ] Verify current praesidium RAIDZ2 health, historic degraded/power cable concern, scrubs, SMART, and alerts.
- [ ] Establish independent versioned backups for speedpool (single NVMe; no redundancy), including appdata and critical database/VM data.
- [ ] Resolve previously failed TrueNAS cloud-sync/MEGA backup and confirm a recent offsite recovery point.
- [ ] Audit backup coverage for TrueNAS config, ZFS, AMP VM/game worlds, Docker Compose/secrets stored privately, ARR data, Immich originals/database, and HomePi.
- [ ] Test an actual restore of HomePi and selected Servius data to an isolated environment.
- [ ] Limit backup deletion exposure; verify snapshot retention and independent/offsite recovery copies.

## P1: Hardening and maintenance
- [ ] Audit public ports, Nginx Proxy Manager routes, DuckDNS, firewall, Tailscale access, and unnecessary WAN exposure.
- [ ] Validate Authentik protections, TLS renewals, CrowdSec/Fail2ban and external access behavior.
- [ ] Audit Docker privileges, mounts, secrets, service accounts and stale containers; test no-new-privileges later from a PC.
- [ ] Review SSH key-only access, least privilege, MFA where possible, and emergency access procedures.
- [ ] Plan TrueNAS SCALE update with config backup, official upgrade guidance, rollback path; do not blindly auto-update TrueNAS, ZFS or VMs.
- [ ] Configure safe security updates where appropriate; stage container/app upgrades with backups, version pinning, health checks, and rollback.
- [ ] Verify HomePi and Servius reboot/power-recovery behavior; monitor DNS, Unbound, Pi-hole, Tailscale, Dockhand, Docker, and critical apps.
- [ ] Verify disk, capacity, CPU/RAM and service-health notifications work end to end.

## P1: HomePi backup and Servius monitoring
- [x] HomePi scheduled ~03:30 systemd backup to Servius, with SQLite checks and remote SHA-256 verified transfer.
- [x] HomePi retention wired after verification: 14 daily slots, four weekly age buckets, protected initial archive and newest copy; deletion behavior still needs testing.
- [ ] **On Servius existing Uptime Kuma WebUI only:** create HomePi - Daily Backup Push monitor; ~25-hour heartbeat window and notifications. Wire HomePi success heartbeat *after* checksum-verified backup and retention. Test missed/failed backups. Do not install Kuma on HomePi.
- [ ] Safely test retention with disposable expired archive examples, exact directory guard, and refine weekly buckets/calendar logic.
- [ ] Reassess 5-minute backup timeout with growth/network variability; verify first unattended overnight run and missed-run recovery.
- [ ] Centralize all backup/storage/service alerts in existing Servius monitoring; add HomePi Uptime Kuma monitor to Homepage.

## P2: Work out existing kinks
- [ ] Gradually standardize Docker stack management in Dockhand, preserving Compose locations and homepage.env editing before removing Portainer/Dockge.
- [ ] Confirm and fix remaining issues (if still present): Homepage 403, Seerr/Jellyseerr authentication, Bazarr configuration, unhealthy containers, broken proxy routes.
- [ ] Audit ARR stack paths, import/hardlink reliability, downloader jobs, permissions, recovery and selective upgrades.
- [ ] Ensure AMP VM and hosted-game startup, networking, VM/world backups and appropriate resource limits; revisit Project Zomboid stutter only if reproducible.
- [ ] Review disconnected Kioxia secondary boot SSD / potential TrueNAS boot mirror after core storage is healthy.
- [ ] Refresh dated service inventory, actual config paths, recovery runbooks and GitHub documentation after changes.

## P3: Future projects after stable baseline
- [ ] Move approximately 4 TB personal storage from h711nt to Servius after datasets/ACLs/backups are ready; use two available approximately 5 GbE PCIe adapters during bulk migration.
- [ ] Phone-friendly Wake-on-LAN for Servius and h711nt; check Raspberry Pi hardware/power-state limitations for HomePi.
- [ ] DJI workflow for ingest, originals, catalog/browsing, workstation editing and exported media on Servius.
- [ ] Expand Immich photo management after confirming recoverable originals and database backups.
- [ ] Evaluate FileBrowser Quantum, Filestash and SFTP for safe remote file management and AMP uploads.
- [ ] Improve Homepage dashboard and Roman-themed service icons once operations are stable.
- [ ] Assess additional reliable services, ARR enhancements and hardware/storage expansion only after P0/P1.

## Planned application deployments (current shortlist)

These are deployment candidates, not assumed installed. Evaluate compatibility, maintenance, overlap with existing services, security, storage paths, backup and rollback before deploying one by one in Dockhand.

- [ ] CrowdSec — verify/improve existing deployment and proxy integration.
- [ ] Recyclarr / Configarr — compare and select ARR profile/config synchronizer.
- [ ] Decluttarr / Cleanuparr — review existing Decluttarr and evaluate replacement or complement without conflicting cleanup.
- [ ] Recommendarr — research recommendation automation.
- [ ] Maintainarr — assess rules-based library maintenance; test dry-run and exclusions.
- [ ] Qui — assess torrent-management interface.
- [ ] Tdarr — **retired**; revisit only if the operator explicitly decides to redeploy (old images removed 2026-10-15).
- [ ] Automate GitHub documentation with review and secret protection.
- [ ] SponsorBlockTV — check compatibility with playback environment.
- [ ] Beszel — compare lightweight host metrics to existing Kuma/Grafana tools.
- [ ] Replace Lidarr — compare maintained alternatives and migration.

### Older future ideas
- [ ] Grafana/Prometheus — assess existing deployment before completing monitoring dashboards.
- [ ] Purchase domain and evaluate Cloudflare proxy/tunnel architecture.
- [ ] Ansible — automate repeatable homelab deployments after configurations stabilize.

## Definition of smooth sailing
- [ ] No unknown degraded pools or persistent failed jobs/unhealthy services.
- [ ] Versioned, monitored critical backups with at least one proven restore.
- [ ] Notifications tested for missed backups, down services and storage failure.
- [ ] Core services recover automatically after reboot.
- [ ] Attack surface and privileges minimized and documented.
- [ ] Every component has appropriate update cadence, backup and rollback process.
- [ ] Docs reflect deployed state and expose no secrets.

**Change policy:** Check live health → take backup/snapshot → change one thing → verify → document.
