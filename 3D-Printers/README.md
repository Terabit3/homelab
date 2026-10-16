# 3D Printers — inventory, operations and recovery

This section documents physical 3D printers and printing workflows. It is separate from server applications because the machine, tooling, material and calibration must be recorded to recreate a successful setup.

**Status (2026-10-16):** printer models, firmware, slicer, materials and network integrations have **not yet been verified** in this repository. Do not treat blanks as installed devices.

## Printer inventory

| Printer name | Make and model | Location | Connection | Firmware | Status |
|---|---|---|---|---|---|
| To document | To verify | To verify | USB / LAN / Wi-Fi / SD — verify | To verify | Unknown |

For **each printer**, create a single page only after its exact model is known. Record serial number privately if needed, nozzle/hotend and extruder type, build surface, motion system, print volume, accessories, and modifications. Keep the manufacturer manual and replacement part numbers linked from that page.

## Print workflow

| Component | What to record |
|---|---|
| 3D design | CAD software and where original editable projects are stored/backed up |
| Slicer | App/version, per-printer profile, start/end G-code, nozzle size and material presets |
| Firmware / controller | Firmware family/version, where config and a safe firmware backup live |
| Printer connectivity | Local USB, SD, network, print-server or cloud workflow; specify host if self-hosted |
| File storage | Source CAD, STL/3MF, exports, calibration test files, where data is backed up |
| Filament | Materials, brands and practical temperature/drying settings for proven profiles |
| Upgrades and maintenance | Installed hardware, part numbers, belt/bed condition, lubrication and replacement dates |

Do not publish printer-control tokens, LAN credentials or cloud account sessions.

## Daily operations

1. Verify machine identity, correct bed/nozzle surface and filament type before slicing.
2. Select the matching verified slicer profile and check printer, nozzle, temperatures, supports and first-layer configuration.
3. Check build plate cleanliness and axes for obstructions; preheat and home using the printer's correct manual.
4. Watch the first layer and stop if adhesion or motion is unsafe; do not leave an unproven modification running unattended.
5. Save repeatable successful settings in the printer's page; note failures and the actual fix.

## Calibration and troubleshooting

Document per-printer procedures and verified values for:
- Bed tramming/mesh and Z offset, first-layer adhesion
- Extruder calibration and flow where applicable
- Temperature, cooling, stringing/retraction and dimensional accuracy
- Motion, belts, bearings and unusual sounds
- Filament moisture, clog and under-extrusion checks

Avoid copying another machine's configuration or firmware without confirming board/revision and printer model.

## Rebuilding after losing hardware or configuration

1. Identify the printer make/model and installed modifications from photos and purchase documentation.
2. Recover slicer profiles, editable CAD projects, 3MF project files and firmware/config backups.
3. Restore only firmware/configuration suitable for the exact machine electronics and accessories.
4. Verify endstops, homing, travel bounds and temperature-sensor readings before heating or printing.
5. Perform initial bed setup and a small calibration print; compare results with known successful settings.
6. Export and independently back up the working profiles and firmware configuration.

**Recovery status:** No tested printer rebuild or verified backup set is documented yet.

## Documentation links

- [Repository home](../README.md)
- [Hardware references](../Infrastructure/README.md)
- [Backup and restore](../Operations/Backup-and-Restore.md)
- [Operations overview](../Operations/README.md)

### Information needed to complete this section

Printer make/model(s), upgrades, control firmware, slicer and usual filament types; whether printing services run on Servius, HomePi or a standalone printer controller; and where print projects and profiles are stored.
