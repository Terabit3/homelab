# Other Apps

**Host(s):** Catalog only — multiple hosts  
**Observed deployment:** This entry is a documentation index and is not a container or Compose project.

## What it does
Captures miscellaneous or experimental services not requiring a separate full guide.

## Dependencies and data
Use main [application index](../README.md) and [Services](../../Services.md) for each real application's host/ownership; proposed entries in ROADMAP are not installed.

## Practical operations
When adding a real deployment, record its host and owner first; if it has substantial configuration, create a dedicated README. Separate paused/standby service state from experiments.

## Safe maintenance
Verify live container identity, mounts, network, pool health and recoverable backup before any change. Use the owning manager (TrueNAS Apps or Dockhand) and avoid duplicate deployments. Preserve data and confirm intended user-facing behavior after updates.

## More information
[Application index](../README.md) · [Host/service inventory](../../Services.md) · [Current Servius handoff](../../Assistant-Notes/Servius.md) · [Operations](../../Operations/README.md)

_Evidence is dated Oct 2026, not a live status check. Never put secrets or complete .env files in GitHub._
