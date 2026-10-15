# VS Code & Git — Quick Reference

Common shortcuts and Git conventions for maintaining my homelab documentation.

## VS Code shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + Shift + V` | Open formatted Markdown preview |
| `Ctrl + K`, then `V` | Open Markdown preview beside the editor |
| `Ctrl + Shift + E` | Open File Explorer |
| `Ctrl + Shift + G` | Open Git Source Control |
| `Ctrl + \`` | Show or hide the integrated terminal |
| `Ctrl + P` | Quickly find and open a file |

**Note:** These shortcuts apply to VS Code on Windows. Some behave differently when the integrated terminal has focus.

## Git commit prefixes

| Prefix | Purpose | Example |
|---|---|---|
| `docs:` | Documentation changes | `docs: update Servius README` |
| `feat:` | New functionality | `feat: add automated AMP backups` |
| `fix:` | Fixes | `fix: correct Syncthing permissions` |
| `refactor:` | Reorganization without changing functionality | `refactor: reorganize Docker stacks` |
| `chore:` | Routine maintenance | `chore: update container versions` |
| `security:` | Security-related changes | `security: restrict backup SSH access` |
| `backup:` | Backup changes | `backup: add 14-day retention` |

These are personal commit conventions; Git doesn't enforce them automatically.

## Common Git commands

**See changed files**

`git status --short`

**Review changes**

`git diff`

**Stage a specific document**

`git add "Applications/ARR Stack/README.md"`

**Commit staged changes**

`git commit -m "docs: update ARR troubleshooting"`

**See recent commits**

`git log --oneline -10`

**Send committed changes to GitHub**

`git push`

**Important:** Review staged changes with `git diff --cached` before committing, especially when importing configurations. Never commit passwords, API keys or private `.env` files.

## Documentation workflow

1. Open the relevant application folder in VS Code.
2. Update its README or add a troubleshooting document.
3. Include the problem, cause, working fix and verification steps.
4. Review any commands and remove private information.
5. Commit the changes with the appropriate prefix.
6. Push to GitHub after reviewing the changes.