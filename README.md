# OrcaSlicer INDX Generic - basic manual setup

Experimental OrcaSlicer build for Klipper/Kalico printers with the official Bondtech INDX macros already installed. This is a starting profile to adapt to your machine, not a universally validated printer configuration. Physical printing has not yet been validated by this project.

## Choose your setup guide / Scegli la lingua

| Language / Lingua | Read online | Download PDF |
| --- | --- | --- |
| English | [English setup guide](SETUP_GUIDE_EN.md) | [English PDF](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.1-preview.20261018/Orca_INDX_Generic_Setup_Guide_EN.pdf) |
| Italiano | [Guida in italiano](GUIDA_CONFIGURAZIONE.md) | [PDF italiano](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.1-preview.20261018/Guida_configurazione_Orca_INDX_Generic.pdf) |

Both guides explain tool count, nozzle diameters, required printer macros, start/tool-change/end G-code and first slicing checks. Profile and launcher filenames remain as supplied, including their Italian names.

## Downloads

Open the [release page](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/tag/v0.3.1-preview.20261018), scroll down and expand **Assets**.

- [Windows x64 installer (.exe)](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.1-preview.20261018/OrcaSlicer-INDX-Generic-0.3.1-preview-Setup-x64.exe): installs the application, initial profiles and both guides, with separate Start menu shortcuts and an uninstaller.
- [Portable Windows x64 ZIP](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.1-preview.20261018/OrcaSlicer-INDX-Generic-Windows-x64.zip): extract the entire folder to a writable location and run **Avvia-Orca-INDX.cmd**. Do not run it inside the ZIP.
- [Profiles only](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.1-preview.20261018/profili-indx-generici.zip): printer, process and PLA JSON files.
- English and Italian PDF/text guides: choose your language above or download from Assets.

**Source code** archives contain source material, not the ready-to-run Windows application. The installer and portable ZIP contain the same updated application and guides. See [Windows installation notes](WINDOWS_INSTALLER.md).

## What is included

A printer profile with four initial 0.4 mm logical tools, a manual 0.20 mm process and a stock PLA profile to calibrate. Change tool count and nozzle diameters in the native printer settings. Tool changes use:

```gcode
CHANGE_TOOL TOOL={next_extruder} TEMP={new_filament_temp}
M400
```

The output treats the INDX feeder and heater as one physical unit. There is no automatic filament synchronization, custom touch library or custom INDX Auto slicing optimization. No custom KlipperScreen plugin is required.

This build shares its software base with the SV08 Max project. The `INDX_BASIC_PROFILE` marker disables the extended services for this profile. `INDX_PRINT_START` appears only in a comment to enable shared-feeder output; it does not call that custom macro. Keep these markers when adapting the start G-code.

## Before printing

1. Replace the example **200 x 200 x 200 mm** volume, per-tool reachable areas and motion limits with your printer's real values.
2. Set the actual tool count, nozzle diameters and filament-to-tool assignments. Apply physical offsets in the firmware; avoid applying them a second time in the slicer.
3. Add your machine's verified start, homing, nozzle-cleaning and calibration sequence. The template does not contain a universal start sequence.
4. Verify the official Bondtech macros, docks and offsets. This profile does not install firmware, pin assignments or motion configuration.
5. Set your real filament parameters, then inspect Preview and exported G-code before commissioning a print.

To import the profiles into an existing compatible build, extract `profili-indx-generici.zip` and use **File > Import > Import Configs** for the printer, stock PLA, base process and the desired nozzle/layer JSON files. Default values are not calibration results.

## Installation and user data

The installer runs for the current Windows user and uses a separate application directory from the SV08 Max edition and official OrcaSlicer. Its shortcut opens the included `dati` folder as its data directory. Initial profiles are seeded only when that directory has no OrcaSlicer configuration. Updates preserve existing settings, and uninstall leaves user data in place. Portable and installed copies do not automatically migrate or synchronize their settings.

The installer does not install firmware, connect to a printer, alter KlipperScreen, associate model file types or launch Orca automatically. No personal printer address, filament library or calibration is bundled. This community preview is not digitally signed.

## Validation and source

The standalone profiles were loaded by the application and used to generate G-code for a 10 mm cube with the expected tool-change commands. This validates software slicing, not physical hardware.

`UPSTREAM.json` identifies the exact official Orca source revision. `OrcaSlicer-INDX.patch` contains the changes corresponding to the latest installer binary. The original portable ZIP has its matching source in the release tag. Run `python prepare_source.py` to reconstruct the source in a new directory, then build using Orca's official toolchain and dependencies. The AGPL-3.0 license and corresponding source material are included in the application packages. `windows-installer.iss` contains the installer recipe; see [build and installation notes](WINDOWS_INSTALLER.md).

The full [SV08 Max and KlipperScreen edition](https://github.com/Moffy97/OrcaSlicer-INDX) remains in a separate repository. See the [official Bondtech INDX instructions](https://github.com/BondtechAB/INDX#slicer-setup). This is not an official OrcaSlicer or Bondtech release.

## Nozzle and layer presets

The separate [nozzle/layer package](profili-ugello-layer.zip) contains 40 manual process presets. Import the families you need and set the actual nozzle diameters in your printer profile; selecting a process does not change the hardware or choose a physical tool. No custom sync, automatic optimization or KlipperScreen plugin is required.

| Nozzle (mm) | Included layer heights (mm) |
| --- | --- |
| 0.20 | 0.06, 0.08, 0.10, 0.12, 0.14 |
| 0.25 | 0.05, 0.06, 0.07, 0.08, 0.10, 0.12, 0.14, 0.16, 0.17 |
| 0.40 | 0.10, 0.12, 0.14, 0.16, 0.20, 0.24, 0.28 |
| 0.60 | 0.16, 0.20, 0.24, 0.28, 0.30, 0.32, 0.36, 0.40, 0.42 |
| 0.80 | 0.20, 0.24, 0.28, 0.30, 0.32, 0.36, 0.40, 0.42, 0.48, 0.56 |

These are conservative starting profiles, capped at 70% of nozzle diameter. The 0.25 family uses a 0.12 mm first layer; 0.05 mm layers need physical validation on the target printer. Generic 0.20 mm presets require an actual compatible 0.20 mm nozzle; they do not imply Bondtech offers that INDX tool. Existing installers remain unchanged; import this optional package into an installed copy.
