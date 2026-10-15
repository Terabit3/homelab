# Homepage — Servius

## What it does

Homepage is my main dashboard for accessing and monitoring homelab applications.

It runs as a Docker container on Servius.

Official documentation: https://gethomepage.dev/

## Why I use two URLs for each application

I use separate addresses for opening applications and monitoring their status.

- **External URL (`href`):** The address I click to open an application, usually through Nginx Proxy Manager.
- **Internal URL (`siteMonitor`):** Connects directly to the application for status monitoring.

This means Homepage can continue checking an application's status even if the reverse proxy is unavailable.

Example:

```yaml
- Media:
    - Sonarr:
        href: https://sonarr.example.com
        siteMonitor: http://sonarr:8989
```

These addresses are examples, not my actual configuration. Internal container names work only when Homepage can reach them over the appropriate Docker network.

## Configuration locations

**Inside Docker container:**

`/app/config`

**On Servius:**

/mnt/speedpool/appdata/homepage/config

**Private credentials:**

`E:\Homelab-Private\Servius\homepage\`

Verify that this private backup is populated before relying on it for recovery.

## Environment variables and secrets

Homepage supports substituting environment variables inside its configuration files.

For sensitive widget credentials, use variables starting with:

- `HOMEPAGE_VAR_` — reads a value from the container environment.
- `HOMEPAGE_FILE_` — reads a value from a mounted file.

Example:

```yaml
key: "{{HOMEPAGE_VAR_EXAMPLE_API_KEY}}"
```

The variable must be available inside the running Homepage container. Having it in a host `.env` file alone does not guarantee that Docker passes it into the container.

Never commit actual API keys or passwords.

## Useful commands

Run on Servius SSH.

Check container status:

```bash
sudo docker ps --filter name=homepage
```

View recent logs:

```bash
sudo docker logs --tail 100 homepage
```

Find the actual configuration directory:

```bash
sudo docker inspect homepage --format '{{range .Mounts}}{{println .Source "->" .Destination}}{{end}}'
```

Show container health:

```bash
sudo docker inspect homepage --format '{{.State.Health.Status}}'
```

## Troubleshooting

### Dashboard opens, but status monitoring fails

1. Check whether the application is running.
2. Verify the internal URL or container hostname.
3. Check whether Homepage can access the appropriate Docker network.
4. Check whether the application requires authentication for monitoring requests.
5. Review Homepage logs.

### Widgets cannot read their API credentials

1. Check the environment-variable name.
2. Confirm the variable is passed into the container.
3. Verify the variable reference in the YAML configuration.
4. Check Homepage logs for errors.
5. Restart or redeploy Homepage if its container environment has changed.

Do not print actual secrets into troubleshooting notes or terminal screenshots.

## Historical note

An older installation used `/opt/homepage/config` and a systemd service with `EnvironmentFile=`.

This procedure does not describe the current Docker deployment on Servius.

## Lessons learned

Separating the clickable reverse-proxy URL from the internal monitoring URL makes the dashboard more useful when troubleshooting reverse-proxy failures.

## To verify

- Actual Servius configuration directory
- Homepage Compose location
- Network connections
- Private environment backup availability
- Current Homepage version
