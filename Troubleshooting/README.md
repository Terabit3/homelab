# Troubleshooting · Known incidents

Start at [START HERE](../START-HERE.md) for an active outage. These articles preserve particular past problems; **historical solutions are not automatically safe on a newer TrueNAS version**.

| Symptom | Guide | Notes |
|---|---|---|
| Supermicro fans surge because RPM falls below IPMI thresholds | [IPMI fan thresholds](Supermicro-IPMI-Fan-Thresholds.md) | Identify sensor and preserve default values before modifying |
| NVMe controller repeatedly resets; `speedpool` disconnects | [NVMe controller reset](TrueNAS-NVMe-Controller-Reset.md) | Kernel power-management workaround was historical |
| Pool DEGRADED, dataset missing, boot trouble, deleted files, network issue | [TrueNAS recovery runbook](../Operations/TrueNAS-Troubleshooting-Runbook.md) | General triage |
| Disk replacement, expansion or boot-mirror work | [Disk / pool guide](../Operations/TrueNAS-Disk-Replacement-and-Expansion.md) | Potential data loss; verify serials |
| Pi-hole reverse proxy shows wrong page | [NPM/Pi-hole historical issue](../Applications/Nginx%20Proxy%20Manager/Pi-hole-Proxy-Redirect.md) | Old Lighttpd-specific workaround; may not apply |

**Document a new fix:** Copy [Incident template](../Templates/Incident.md), name it after the symptom, add it to this table, and record how you confirmed recovery.
