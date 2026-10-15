# homepi — System Reference

> **Status:** Imported from legacy documentation; verify current services and addresses on the Raspberry Pi. Do not assume the old application list is current.

## Purpose and hardware

- Raspberry Pi 3 Model B+ (legacy notes call this “3+”); quad-core ARM Cortex-A53; 1 GB RAM.
- DietPi, a minimal Debian-based OS (verify installed version).
- Headless, low-power home-network services.

## Previously documented services

| Service | Purpose | Verification needed |
|---|---|---|
| Pi-hole | DNS filtering | Confirm active DNS role and admin URL |
| Unbound | Local recursive DNS resolver | **Unbound is not automatically DNS-over-HTTPS**; check actual configuration |
| Tailscale | Remote access | Confirm routes, ACLs and address |
| Portainer | Container management | Confirm still installed and used |
| Uptime Kuma | Monitoring | Confirm whether it still runs here or on Servius |

## Historical connection information

Legacy notes used `ssh pi@192.168.50.26`. Confirm the current SSH account and IP rather than treating this as a guaranteed command.

## Useful checks (run on homepi)

```bash
hostnamectl
ip -brief address
systemctl --failed
```

For DNS checks, document the actual Pi-hole upstream and whether Unbound is recursive, forwarding, or configured for encrypted DNS. Avoid changing DNS servers until the role of both homepi and Servius is clear.

## To document next

- Current IP and Tailscale setup.
- Which node is primary/secondary DNS.
- Backup location and restoration instructions.
- Actual installed services, versions and where their configuration is stored.
