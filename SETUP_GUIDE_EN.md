# Quick setup guide - OrcaSlicer INDX Generic

Manual configuration for your own printer | 17 October 2026

## 1. Download and set up Orca

This guide covers OrcaSlicer INDX Generic 0.3.0 preview, the basic version with manual configuration. It does not require our KlipperScreen integration and does not automatically synchronize tools or filaments. It does not install firmware or printer macros.

Open the release in the OrcaSlicer-INDX-Generic repository and expand Assets at the bottom. Download OrcaSlicer-INDX-Generic-Windows-x64.zip, extract the ENTIRE folder to a writable location, then run Avvia-Orca-INDX.cmd. Do not run it inside the ZIP. Its data folder is separate from your existing Orca installation.

If you already use this build, you can import just the profiles: extract profili-indx-generici.zip, then use File > Import > Import Configs to import the three printer, process and filament JSON files. If official Orca rejects a setting or lacks mixed-nozzle support, use the application provided in this release.

Select INDX - Generica semplice - DA CONFIGURARE and save it under your own name, such as My printer INDX - 3 tools. The 200 x 200 x 200 mm volume and four 0.4 mm nozzles are placeholders, not your actual machine configuration. The bundled profile and launcher names remain in Italian; use the exact names shown here.

Before proceeding, install and commission INDX according to Bondtech's instructions for your machine. A slicer profile does not establish compatibility with electronics, pins, homing or docks. Keep a copy of your working printer configuration.

## 2. Set tool count and nozzle diameters

Open Printer Settings using the pencil beside the printer profile. Enable Expert visibility if a field is hidden. In the capabilities/extruder section, set the number of logical extruders to match the physical tools. Tab names may vary by language and version.

Configure each Extruder 1, Extruder 2, etc. tab: actual nozzle diameter, layer-height limits and reachable print area. Orca's displayed numbering starts at 1; printer tool commands start at 0.

| Orca tab | Firmware tool | Example diameter |
| --- | --- | --- |
| Extruder 1 | T0 | 0.4 mm |
| Extruder 2 | T1 | 0.6 mm |
| Extruder 3 | T2 | 0.8 mm |

For three tools, configure three logical extruders, not four. Check all per-extruder settings after changing the count. Adding a filament in the sidebar does not add a physical dock or tool to the printer.

If the build offers a Standard/CHT setting per extruder, select the hardware actually installed. Do not increase flow simply because a nozzle is CHT: measure its sustainable maximum volumetric flow and enter that in the filament profile. The generic base does not calculate automatic increases.

Keep Single extruder multi material disabled: this setup has multiple logical tools. Physical offsets should be handled by the INDX macros. Leave slicer XY offsets at zero if the firmware already applies them, to avoid applying compensation twice.

## 3. Required printer macros

Use the official Bondtech macro package matching your installed plugin and firmware. Do not copy SV08 Max pins, coordinates or movement macros to a different machine. This section is a checklist, not a conversion installer.

Official files: indx.cfg for user configuration; indx-tc-macros.cfg for CHANGE_TOOL and PARK_TOOL; indx-cal.cfg for calibration; homing.cfg for the homing sequence, which must suit your machine. These example paths apply only if the files are actually stored in the indx folder:

```ini
[include indx/indx.cfg]
[include indx/indx-tc-macros.cfg]
[include indx/indx-cal.cfg]
# Include only if compatible with your homing configuration:
# [include indx/homing.cfg]
```

In indx.cfg, TOOL_POSITIONS holds variable_tool_count and each tool's variable_tN_x / variable_tN_dock_y coordinates. Tool count and indices must match Orca. Use measured values only: this profile cannot supply universal dock positions.

Check that T0 through T(N-1), CHANGE_TOOL and PARK_TOOL are available, along with all dependencies required by your installed official version. For additional tools, extend the Tn shortcuts using the official pattern. INDX has one physical [extruder]: do not create [extruder1], [extruder2], etc. for passive tools.

Klipper permits only one homing_override. If your machine already has one, integrate the sequences instead of adding a second override. Calibrate docks, tool offsets and the load cell using the manual for your installed version. Never execute tool changes using placeholder dock coordinates.

## 4. Start, tool-change and end G-code

In Printer Settings, open Machine G-code. The Change filament G-code field in our profile already contains:

```gcode
CHANGE_TOOL TOOL={next_extruder} TEMP={new_filament_temp}
M400
```

Keep the braces and variable names exactly as written. During slicing, Orca replaces them with the incoming tool number and its temperature. Do not add a second tool change for the same operation.

The generic start G-code is deliberately incomplete. Preserve these two comment markers, which this build requires:

```gcode
; INDX_BASIC_PROFILE
; INDX_PRINT_START: shared feeder/heater output marker only, NOT a macro call.
```

Below the markers, insert the verified start sequence for YOUR machine: initial tool selection, homing, bed temperature, nozzle cleaning, any leveling/mesh routine, heating and priming in the appropriate order. If you use PRINT_START, its parameters must match your own macro definition. There is no universal call to paste here.

INDX_PRINT_START appears only inside a comment: you do not need to create that custom SV08 Max macro. Also avoid INDX_PRINT_END or other full-integration macros unless they actually exist on your printer.

The basic end G-code turns off the nozzle, bed and fan using M104 S0, M140 S0 and M107. It does not park a tool or perform final moves. If you want PRINT_END or PARK_TOOL, integrate and verify your own sequence; do not insert generic movement coordinates.

## 5. Filaments, slicing and first checks

Add your actual filaments in the sidebar and assign each part or print feature to the correct tool. If the print dialog offers filament-to-tool mapping, verify it before sending the file: color, filament slot and tool number are different things.

Start with the INDX - Base semplice 0.20 process and save a clearly named copy. For smaller nozzles, reduce layer height and line widths within their limits. A 0.2 mm nozzle must not print 0.4 mm-high layers. The basic version does not automatically choose a mixed-nozzle strategy.

For example, when testing 0.4 and 0.8 mm nozzles, you can start with 0.20 mm layers for both and line widths appropriate to the tool assigned to each feature. Later, optimize combined infill while checking its actual thickness in Preview. Changing a nozzle diameter does not necessarily update every process value that was explicitly overridden.

Set temperature, maximum volumetric flow, flow ratio and pressure advance for the material actually loaded. The supplied PLA values are starting values, not a calibration for your filament. Do not combine different materials without checking adhesion, temperatures and their intended use.

For a first test, use a small model with two regions assigned to different tools. After slicing, inspect tool selection, layer heights, line widths and any prime tower in Preview. Open the G-code and check CHANGE_TOOL calls for valid indices, correct temperatures and a complete start sequence.

The application has been checked through software slicing; your printer's physical compatibility still needs commissioning. Only attempt a supervised print after completing the official dock checks. To add a tool later, update the printer first, then its count, diameter and mapping in Orca.

## 6. Troubleshooting and downloads

| Problem | Quick check |
| --- | --- |
| Unknown command CHANGE_TOOL | Official macros are missing, not included or incompatible with the installed version. |
| Tool does not exist | Compare Orca's extruder count, G-code tool indices and firmware tool_count. |
| Invalid layer or line settings | Review the process and each used extruder's settings. Changing diameter alone is insufficient. |
| Incorrect print start | The template start G-code does not home or prime: add your machine's verified sequence. |
| Incorrect offsets | Check firmware calibration and make sure the slicer does not apply compensation a second time. |
| Cannot find downloads | Open Releases, select the version and scroll to Assets. Source code is not the Windows application. |

Download from Assets: the complete Windows application, separate profiles, the setup guide in PDF/text, and guide checksums. The previously published application ZIP remains unchanged; download the updated guides separately. Choose the file ending in EN.pdf for this English guide.

Project: https://github.com/Moffy97/OrcaSlicer-INDX-Generic
Release: https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/tag/v0.3.1-preview.20261018

Official sources checked on 17 October 2026: Bondtech INDX, Firmware Configuration and Slicer Setup sections; macros/indx.cfg and macros/indx-tc-macros.cfg. Official macros evolve: follow the documentation matching your installed version.

https://github.com/BondtechAB/INDX#slicer-setup
https://github.com/BondtechAB/INDX#firmware-configuration
https://github.com/BondtechAB/INDX/blob/main/macros/indx.cfg

A guide from the OrcaSlicer INDX Generic community project, not an official OrcaSlicer or Bondtech release. The full SV08 Max version with automatic integrations remains in the separate OrcaSlicer-INDX repository.


### Automatic Z and infill grouping

Normal slicing preserves the sidebar layer height. INDX Auto Slice chooses a common layer height independently for each mode and purpose, using active nozzles only. It compares feasible infill grouping boundaries and a half-nozzle candidate with native slicing: time includes tool changes and filament volumetric limits remain active. Surface quality and physical shell thickness follow the selected purpose. The 0.8 mm nozzle ceiling is 0.64 mm; 0.30 mm walls with 0.60 mm infill are included when feasible. Grouping remains local to geometry; solid layers and transitions may require more frequent infill.

Verified motor resolution can be stored as `INDX_Z_STEP=<millimetres>` in printer notes. The SV08 Max profile uses 0.001875 mm, derived from retained Klipper settings: rotation distance 40, ratio 80:12, 200 full steps and 16 microsteps. Other printers must use their own verified mechanics; absent metadata leaves heights unquantized. Microstep size is command resolution, not a claim of physical positioning accuracy.


Automatic optimization jointly enumerates the available wall, solid-surface and sparse-infill tool assignments allowed by each mode. Walls and surfaces retain the primary material and color; sparse infill may use other colors of the same polymer. The search evaluates nozzle-dependent Z candidates for every assignment, including single-tool printing and infill grouping transitions. Conditional material alternatives remain separate and require approval. Native slicing ranks completed valid trials by total time, including tool changes; near ties use fewer changes, then lower filament weight. The comparison shows valid and rejected trial counts, caches each winning mode, and preserves the usual sidebar height for ordinary slicing. This is a discrete candidate search, not a proof of globally optimal layer heights or mechanical strength. Physical commissioning remains necessary.
