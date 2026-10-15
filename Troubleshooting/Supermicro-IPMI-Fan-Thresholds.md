# Supermicro IPMI — Fan Speed Thresholds

## Problem

On a Supermicro motherboard, quiet, low-RPM consumer fans can fall below the BMC's default minimum fan-speed threshold. The BMC may interpret this as a failed fan and repeatedly increase fan speed, causing the fans to surge up and down and creating sensor alerts.

## Why it happens

The factory thresholds often assume higher-RPM server fans. A fan spinning normally at a lower RPM may be flagged as too slow. **Do not lower thresholds until you've checked the fan's normal RPM and confirmed adequate cooling.**

## Requirements

- Access to the Supermicro BMC/IPMI interface.
- `ipmitool` installed on a machine that can reach the BMC, or available on the host.
- The correct fan sensor names and observed idle/load RPMs.

On Debian/Ubuntu, if you are using a suitable administration machine:

```bash
sudo apt update && sudo apt install ipmitool
```

On Alpine:

```bash
sudo apk add ipmitool
```

> **TrueNAS note:** Avoid installing packages directly into a managed TrueNAS system just for this task. Running `ipmitool` remotely from another machine is usually preferable.

## 1. Find the fan sensors

Local access, if supported:

```bash
ipmitool sensor list
```

Remote access over IPMI LAN (prompts for password, rather than exposing it in the command):

```bash
ipmitool -I lanplus -H <BMC-IP> -U <USERNAME> -a sensor list
```

Identify the fan sensors, such as `FAN1`, and note their current RPMs.

## 2. Read the current thresholds

```bash
ipmitool -I lanplus -H <BMC-IP> -U <USERNAME> -a sensor get FAN1
```

Record the existing thresholds so you can restore them. On supported Supermicro BMCs, the three **lower** thresholds are Lower Non-Recoverable (LNR), Lower Critical (LCR), and Lower Non-Critical (LNC).

## 3. Set sensible thresholds

For a fan sensor, `ipmitool` sets **three values per threshold group**, not a single `lower` and `upper` value in one command.

Example **only** — values must suit the actual fan, and some BMCs round to supported increments:

```bash
ipmitool -I lanplus -H <BMC-IP> -U <USERNAME> -a sensor thresh FAN1 lower 100 200 300
```

This example changes only the *lower* thresholds (LNR=100, LCR=200, LNC=300 RPM). Choose thresholds lower than the fan's normal minimum operating speed but high enough to detect a genuine fan failure. Leave upper thresholds alone unless there is a demonstrated problem with them.

## 4. Verify and monitor

```bash
ipmitool -I lanplus -H <BMC-IP> -U <USERNAME> -a sensor get FAN1
ipmitool -I lanplus -H <BMC-IP> -U <USERNAME> -a sel list
```

Confirm the new values persisted, fan speeds remain stable, CPU/system temperatures are safe under load, and genuine fan failures would still generate alerts.

## Notes to retain

- Record which headers/sensors were affected and the **old and new threshold values** before/after changing them.
- The issue may be caused by BMC fan-control behaviour rather than fan hardware failure.
- The thresholds above are illustrative, **not known-good settings for Servius**.
- If a fan actually stalls, changes speed unexpectedly, or temperatures rise, investigate cooling hardware rather than masking the alarm.

## Reference

[TrueNAS community: How to change IPMI sensor thresholds using ipmitool](https://www.truenas.com/community/resources/how-to-change-ipmi-sensor-thresholds-using-ipmitool.35/)

*Migrated from legacy homelab troubleshooting notes; verify the commands and sensor names against the installed BMC firmware before applying.*
