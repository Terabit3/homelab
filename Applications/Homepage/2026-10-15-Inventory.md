# Homepage reconciliation — October 15, 2026

## Reported running TrueNAS Apps

`arr-dashboard`, `calibre-web-automated`, `dockge`, `dockhand`, `filebrowser-quantum`, `immich`, `jellyfin`, `nginx-proxy-manager`, `pihole`, `syncthing`, `tailscale`, `uptime-kuma`.

## Reported running Dockhand stacks

`arr-stack`, `arr-stack-secondary`, `arr-stack-stats`, `authentik`, `boxarr`, `byparr`, `dashy`, `fail2ban`, `homepage`, `kuma-sync`, `nextcloud`. `arr-stack-disabled` was Created; `parrot`, `whisparr`, `windows` were stopped. Container members of stacks not all independently checked.

## Dashboard changes and unresolved issues

- Calibre-Web Automated is the running service; distinguish it from separate standalone Calibre (not confirmed).
- Keep Dockhand and Dockge while migration remains in progress.
- Cards requested for Autobrr, CrowdSec, Home Assistant and HomePi services are not proof those services are currently running; verify actual URLs first. Pi-hole Primary already provides HomePi DNS card. Portainer and Uptime Kuma HomePi ports need `docker ps` checks; Unbound has no normal web interface.
- Keep real values in private `homepage.env`, **never commit**; sample/template values use `HOMEPAGE_VAR_*` and placeholders. To merge in Notepad++ use ComparePlus on backups, optionally sort lines alphabetically.
- TrueNAS Homepage widget returned HTTP **403 Forbidden** (`You do not have permission to access this endpoint`); determine exact failed API and minimal key privileges. Don't simply grant admin rights.
- Additional dashboard cards showing HTTP 500 may be endpoint/widget config rather than crashed apps; audit independently.
