# Editing this handbook

**Write for yourself at 3 a.m. and for someone who does not know the system.** Keep things simple enough to update in VS Code without special tools.

## Where information belongs

| What changed? | File to update |
|---|---|
| New app | `Applications/<App>/README.md` (copy [app template](Templates/Application.md)) and [app directory](Applications/README.md) |
| Error fixed | New `Troubleshooting/<descriptive-name>.md` from [incident template](Templates/Incident.md); add to [index](Troubleshooting/README.md) |
| Disk/cabling/hardware | [Hardware inventory](Infrastructure/Hardware/Hardware-Inventory.md) plus relevant runbook |
| Live container state | Dated [Docker inventory](Snapshots/Docker-Inventory.md); avoid declaring an old snapshot live |
| Changed recovery / backup procedure | [Backup and restore](Operations/Backup-and-Restore.md) |

## Formatting rules

1. Start with **Purpose / Applies to / Last verified / Danger level** for a procedure.
2. Put **STOP conditions** before any potentially destructive step.
3. Mark code blocks with the **system and shell** (`Servius SSH / bash`, `Windows / PowerShell`, or `TrueNAS UI`).
4. Distinguish **observed**, **historical**, **official documentation**, and **needs verification**.
5. For every fix record: **symptoms → diagnosis → exact change → checks → rollback / lessons**.
6. Use relative Markdown links, short sections, and tables only if they make reading easier. Avoid custom HTML, badges, external themes, and unnecessary decoration.
7. Never commit real `.env`, passwords, SSH keys, API tokens, secret URLs, configuration exports or identifying hardware serials.

## Safe change workflow (Windows PowerShell in repository root)

```powershell
git status --short
git diff --check
git add -- <specific-file-path>
git diff --cached --stat
git diff --cached --check
git commit -m "docs: describe the change"
git push origin main
```

Replace `<specific-file-path>` with an actual quoted file path. Check `git diff --cached` to review content when sensitive data could be included.

## Before executing recovery instructions

The guides are **references, not an automatic authorization**. Match TrueNAS documentation to the actual installed version, check live ZFS state and backups, and verify the physical disk identity. If unsure, stop.
