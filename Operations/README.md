# Operations — Servius and HomePi

**Dual purpose:** a day-to-day operations reference **and** a step-by-step entry point for rebuilding the homelab after hardware loss or loss of familiarity with the setup. Pick the task below. Historical captures are not current host state.

| Task | Canonical document | Covers |
|---|---|---|
| **Something broken right now** | [Emergency triage](../START-HERE.md) | First checks and stop conditions |
| **Quick host health / Docker checks** | [Servius quick reference](Servius-Operations-Quickref.md) | Read-only commands, managers, immediate warnings |
| **Find a filesystem path** | [Important paths](Important-Paths.md) | Servius, HomePi, AMP VM, h711nt |
| **Check protection or restore data** | [Backup and restore](Backup-and-Restore.md) | Risk, confidence, AMP, appdata and TrueNAS |
| **Restore HomePi or check its backup** | [HomePi backup runbook](HomePi-Backup-and-Restore.md) | Timer, retention, original SHA-256 baseline, restore test |
| **TrueNAS host, app or dataset fails** | [TrueNAS troubleshooting runbook](TrueNAS-Troubleshooting-Runbook.md) | Pools, snapshots, boot, networking, Docker |
| **Replace a disk or expand storage** | [Disk replacement and expansion](TrueNAS-Disk-Replacement-and-Expansion.md) | Identification, RAIDZ2, replace and boot-mirror precautions |

## Rebuilding or rediscovering the homelab

Start with [Rebuild and Rediscovery](Rebuild-and-Rediscovery.md) when you do not remember the system or need to restore it on replacement hardware. It explains how to identify hosts, protect existing data, locate backups and private configuration, select the correct deployment owner, and restore services in dependency order. Follow individual runbooks for executable recovery details; do not assume a dated Compose example is production-ready.

## Other sources, with distinct roles

- [Service and host index](../Services.md) — what runs on which host and who owns it.
- [Applications](../Applications/README.md) — application-specific operations and dependencies.
- [Servius current handoff](../Assistant-Notes/Servius.md) — ongoing migration decisions and next steps.
- [Roadmap](../ROADMAP.md) — future work, not deployed changes.
- [Changelog](../CHANGELOG.md) — dated changes and verification.
- [Historical reports](../Archive/README.md) — previous captures, including the Oct 14–15 operations recap.

## Standard change sequence

**Read-only state → identify exact host and owner → assess pool/backup health → establish rollback → one controlled change → validate service, files and monitoring → document outcome.**

**Current known storage blocker (Oct 16 evidence):** `praesidium` RAIDZ2 DEGRADED (one UNAVAIL member); `speedpool` single NVMe, no redundancy. Neither local snapshots nor a successful live-file sync guarantee independently recoverable database backups. Check live state before risk.

**Boundaries:** These documents contain commands for different machines and software versions. Never run a VM path on TrueNAS or redeploy TrueNAS `ix-*` Apps as ordinary Compose stacks. Do not share populated `.env`, passwords, backup tokens or full credential-bearing inspect output.
