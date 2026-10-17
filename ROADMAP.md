# Homelab Roadmap / TODO
Updated: 2026-10-17

**Current priority:** Harden Servius and HomePi, eliminate operational faults, establish reliable automated backups, safe updates, alerting, and recovery. This is a planning checklist, not a live status report. Never commit credentials, .env contents, SSH keys, or Uptime Kuma Push tokens.

## October 16–17 completed audits and fixes
- [x] Kuma Container Sync: persistent patched `monitor.py` mounted read-only; one-off/TrueNAS init jobs excluded and 10-minute running threshold; production sync succeeded (43 eligible, 71 monitors). Audit found zero stale/temporary Docker monitor cleanup candidates. [Servius handoff](Assistant-Notes/Servius.md).
- [x] Dockhand Authentik Compose EACCES resolved: top-level directory root:apps 0775 and `compose.yml` root:apps 0664; operator confirmed successful Save. No recursive permission change.
- [x] Inventory production ARR mounts and ports, verify five staged `MEDIA_PATH` values, validate staged network relationships, and add Houndarr `/data` mount to staged `arr-utilities` (`docker compose config -q` PASS). No production migration. [Manifest](Operations/ARR-Dockhand-Migration-Manifest.md) and [rollback runbook](Operations/ARR-Data-Migration-Rollback.md).
- [x] Read-only ZFS capacity audit: `praesidium` 51% RAW allocation (22.5T/43.6T), `arr_common` 10.4T logical dataset usage, existing backup datasets only 8.82G; media snapshots modest. No evidence that backup copies explain half the pool.
- [ ] Investigate `praesidium/appdata/arr_common/downloads` 2.21T, preserving active seeding and original files; review only, no deletion while degraded.
- [ ] Verify Immich's live data mounts and history: 457G under praesidium and 202G under speedpool; do not assume duplicates or delete.
- [ ] Adopt selective backups: primary copy of replaceable media, optional second for rare material; prioritize independent copies of irreplaceable photos, documents, configs, database dumps, AMP saves. Snapshot retention unchanged pending measured benefit.

## October 15 operational follow-ups
- [x] Retire Parrot Docker deployment and all its project configuration; preserve WinApps and Whisparr.
- [x] Remove 340 unused historical Docker images after manifest preview, retain container images and one unused rollback candidate per repository; leave Docker volumes untouched.
- [x] Repair shell home and persistent baby-blue Bash prompt for TrueNAS user `timhu`.
- [x] Disable broken legacy `speedpool/ix-apps` snapshot cron; native ZFS snapshot tasks remain enabled.
- [x] Repair Kuma Docker host association and assign Discord notifications to Lidarr and Dockge; verify sync healthy.
- [x] Remove stale Parrot Kuma monitor ID 63 in WebUI (operator-confirmed 2026-10-15); verify it is not recreated by the next container-sync cycle.
- [ ] Inspect 354 unreferenced Docker volumes **without pruning**; review Dockhand image-update policy.
- [ ] Run next manual Docker image/rollback cleanup review around **2027-01-13** (~90 days). No automatic pruning.
- [ ] Restore RAIDZ2 missing member after ordered SATA power cable arrives; verify pool and resilver, then scrub per safe maintenance plan.
- [ ] Restore offsite protection: MEGA cloud sync tasks currently disabled, one last state FAILED; speedpool replication task currently disabled.
- [ ] Revisit TrueNAS update 24.10.2.3 → 24.10.2.4 after config backup/health verification.
- [ ] Migrate `timhu` SSH from password override to tested public-key-only access via TrueNAS supported settings.

## P0: Storage and recoverability
- [ ] Verify current praesidium RAIDZ2 health, historic degraded/power cable concern, scrubs, SMART, and alerts.
- [ ] Establish independent versioned backups for speedpool (single NVMe; no redundancy), including appdata and critical database/VM data. Oct 17 audit: snapshots active, replication task #6 disabled/unassociated, receiving dataset effectively empty. Prepare separate appdata/VM replication after RAIDZ2 repair.
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
- [x] Wire HomePi success-only Uptime Kuma Push heartbeat *after* checksum-verified backup and retention; Oct 16 logs show `KUMA_PUSH_OK`. No extra Kuma on HomePi.
- [ ] Verify Servius Kuma monitor actually received heartbeat, notification route, ~25-hour timeout and missed/failed-backup alert; do not recreate already configured Push URL.
- [ ] Safely test retention with disposable expired archive examples, exact directory guard, and refine weekly buckets/calendar logic.
- [ ] Reassess 5-minute backup timeout with growth/network variability; verify first unattended overnight run and missed-run recovery.
- [ ] Centralize all backup/storage/service alerts in existing Servius monitoring; add HomePi Uptime Kuma monitor to Homepage.

## P2: Work out existing kinks
- [ ] Gradually standardize Docker stack management in Dockhand, preserving Compose locations and homepage.env editing before removing Portainer/Dockge. [Migration manifest](Operations/ARR-Dockhand-Migration-Manifest.md) drafted Oct 17; mounts and networks reconciled Oct 17, Houndarr staged fix validated; still requires final path/ACL, database backups, and healthy storage.
- [ ] Confirm and fix remaining issues (if still present): Homepage 403, Seerr/Jellyseerr authentication, Bazarr configuration, unhealthy containers, broken proxy routes.
- [ ] Audit ARR import/hardlink reliability, downloader jobs, permissions, recovery and selective upgrades. Mount/port/network inventories already documented; do not repeat unless changed.
- [ ] Ensure AMP VM and hosted-game startup, networking, VM/world backups and appropriate resource limits; revisit Project Zomboid stutter only if reproducible.
- [ ] Review disconnected Kioxia secondary boot SSD / potential TrueNAS boot mirror after core storage is healthy.
- [ ] Refresh dated service inventory, actual config paths, recovery runbooks and GitHub documentation after changes.

## P3: Future projects after stable baseline
- [ ] Move approximately 4 TB personal storage from h711nt to Servius after datasets/ACLs/backups are ready; use the **planned dedicated 10Gb SFP+ DAC link** (Broadcom BCM57810S dual-port at Servius, Intel 82599ES single-port at h711nt). [Procurement, cooling, configuration and recovery plan](Infrastructure/Networking/Servius-h711nt-10GbE.md). **Not installed or tested.**
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

## Uptime Kuma expansion — inventory verified October 17
- [x] Inventory existing Servius Kuma monitors read-only: **71 total**, comprising 50 Docker, 16 HTTP, 2 Ping, 2 Push and one group. Existing Home-Pi Ping ID 20, HomePi daily backup Push ID 83, AMP daily backup Push ID 77. Docker monitors grouped under `Servius - Docker Containers` ID 26. All entries active, not necessarily healthy.
- [ ] Audit actual HomePi Docker containers, systemd, local HTTP ports, Pi-hole DNS and Unbound, then add **nonduplicate** checks from Servius Kuma. Do not deploy a second Kuma.
- [ ] Audit monitor status and notification assignments; test Discord delivery and failure recovery, including backup Push missed-heartbeat behavior.
- [ ] Add host-side scoped Push monitoring for Servius ZFS pool status, SMART/capacity and failed snapshot/replication tasks; validate scripts before scheduling or alerting.
- [ ] Add relevant AMP VM and game health/availability checks while preserving existing backup Push monitors.
- [ ] Review historical Docker Kuma entries for TrueNAS one-off/init/permissions/upgrade jobs; existing initial monitors are not deleted by the active Kuma Sync filter. Never bulk-delete without verified candidates.

### HomePi Kuma rollout verified — October 17, 2026
- [x] Servius-to-HomePi reachability: Pi-hole HTTP 8080, Dockhand 3000, Kuma 3001 all HTTP 200; Pi-hole DNS 53 and Unbound 5335 both resolved `example.com`; TCP SSH 22 and other service ports reachable.
- [x] HomePi Docker inventory: Dockhand and local Uptime Kuma v1 containers both running/healthy; Pi-hole, Unbound, Tailscale, Docker, Dropbear and backup timer active; no failed systemd units.
- [x] Added six **active** checks in Servius Kuma 2.5.6 using isolated Python `uptime-kuma-api2` 2.9.0: ID84 Pi-hole HTTP, ID85 Dockhand HTTP, ID86 HomePi Kuma HTTP, ID87 Pi-hole DNS, ID88 Unbound DNS, ID89 SSH TCP. Existing ID20 HomePi Ping and ID83 daily-backup Push preserved. Monitor count **77** after additions. `active` does **not** establish live UP health.
- [ ] Verify status/heartbeats for all eight HomePi checks, check inherited or explicit Discord notifications, and test one non-destructive alert path before declaring coverage complete.
- [ ] Review whether HomePi local Kuma is deliberately retained as auxiliary; no service changed or removed.
- [ ] Do not use the existing `uptime-kuma-api` 1.2.1 inside Kuma Sync to add monitors to Kuma 2.5.6 (`monitor.conditions` NOT NULL error). Tested isolated api2 client via temporary `python:3.11-slim` container; leave production Kuma Sync patch unchanged. Native TrueNAS Python venv attempt failed due missing ensurepip; do not apt-install packages into appliance OS.

- [x] HomePi tag ID 3 applied to all eight Servius Kuma monitors (existing ID20 plus IDs83–89), Oct 17. IDs 20,84–89 all reported UP in 2h beat window. ID83 backup Push showed no heartbeat in 2h window—expected possible for daily schedule; check 48h receipt and missed-heartbeat alert before completion.

### Kuma functional tagging verified — October 17, 2026
- [x] Reused existing host tags `Servius`, `Home-Pi`, `3D Printer`; created `Infrastructure` (ID6), `Media` (ID7), `ARR` (ID8), `Monitoring` (ID9), `Storage` (ID10), `Backup` (ID11), `Game Servers` (ID12). Existing `DEPRECIATED` left unchanged. No manager-specific replacement tag, to remain neutral across Dockhand/Arcane.
- [x] Applied **147 tag assignment changes** to **80** existing Kuma monitors/groups, including the newly present 3D printer monitors/groups IDs 90–91 and HomePi Docker group ID92. Zero errors. Removed obsolete `Dockge` tag ID2 after verifying no remaining assignments. No monitor or service deletion/restart.
- [ ] Consider functional tag refinement for Jellystat/Wizarr (currently ARR as assigned), Syncthing backup usage, and temporary one-off Docker monitor cleanup. Do not assume three recently discovered monitors were created by this tagging operation.
- [ ] Evaluate Arcane Docker manager as a **proposal only**, with review of Docker socket security, migration and competing manager ownership; do not deploy or retire Dockhand unless approved.
