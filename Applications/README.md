# Applications — host and service index

**Start here to identify an app, its host, and the relevant guide.** These are dated configurations, not live monitoring. The [current Servius handoff](../Assistant-Notes/Servius.md) records migration plans separately from deployed state.

| Application | Host | Management | Documentation |
|---|---|---|---|
| ARR (Sonarr, Radarr, Prowlarr, qBit etc.) | Servius | Dockhand / Compose | [ARR overview](ARR%20Stack/README.md) |
| Authentik | Servius | Compose | [Guide](Authentik/README.md) |
| Bazarr | Servius | ARR Compose | [Subtitle provider troubleshooting](Bazarr/README.md) |
| Calibre-Web-Automated | Servius | TrueNAS Apps | [Guide](Calibre-Web-Automated/README.md) |
| Dashy | Servius | Compose | [Guide](Dashy/README.md) |
| Dockhand | Servius + HomePi | TrueNAS Apps / Docker respectively | [Guide](Dockhand/README.md) |
| Fail2ban | Servius | Compose | [Guide](Fail2ban/README.md) |
| FileBrowser Quantum | Servius | TrueNAS Apps | [Guide](FileBrowser%20Quantum/README.md) |
| Homepage | Servius | Compose | [Guide and config](Homepage/README.md) |
| Immich | Servius | TrueNAS Apps | [Guide](Immich/README.md) |
| Jellyfin | Servius | TrueNAS Apps | [Guide](Jellyfin/README.md) |
| Nextcloud | Servius | Compose | [Guide](Nextcloud/README.md) |
| Nginx Proxy Manager | Servius | TrueNAS Apps | [Guide](Nginx%20Proxy%20Manager/README.md) |
| Pi-hole | Servius + HomePi | TrueNAS Apps / DietPi | [Guide](Pi-hole/README.md) |
| Syncthing | Servius + h711nt | TrueNAS Apps / Windows | [Guide](Syncthing/README.md) |
| Tailscale | Servius + HomePi | TrueNAS Apps / DietPi | [Guide](Tailscale/README.md) |
| Uptime Kuma | Servius + HomePi | TrueNAS Apps / Docker | [Guide](Uptime%20Kuma/README.md) |
| Windows / WinApps | Servius (stopped at last snapshot) | Compose | [Guide](Windows/README.md) |
| Other applications | Verify individually | Mixed | [Other apps](Other%20Apps/README.md) |

## How to use this index
- **What runs where?** Use the host and management columns above.
- **What is actually up right now?** Check the relevant host (for Servius, use `sudo docker ps -a` and TrueNAS Apps); dated snapshot in [Services.md](../Services.md).
- **What was recently fixed or planned?** [Assistant handoff](../Assistant-Notes/Servius.md) and [Changelog](../CHANGELOG.md).
- **How do I recover it?** [Operations](../Operations/README.md) or [START-HERE](../START-HERE.md).

Don't create another stub page when one short entry here is sufficient. Reserve per-app READMEs for practical troubleshooting, paths, dependencies and recovery procedures. [Application template](../Templates/Application.md).
