# K1C CFS Mainsail Integration (CFS-C / Next-Gen Patch)


## ✨ K1 2025 (CFS-C & Spool Holder) Fork Enhancements (@hmsGhostship)
* **5-State Filament & Buffer Logic:** Accurate real-time state handling across `Empty`, `Loaded in Hub`, `Loaded to Extruder`, `Spool Holder Active`, and `Printing / Feeding`.
* **Automatic Material & Purge Temperature Sync:** Pulls active material temperatures (`extrude_temp` / `nozzle_temperature`) directly from the selected CFS-C slot or Spool Holder profile for load, unload, and purge operations.
* **Spool Holder Mode (Non-CFS / Fallback):** Dedicated spool holder slot support when printing without CFS-C or using the external spool holder alongside CFS-C.
* **Hardware & Sensor Detection:** Live integration with toolhead filament sensors (DXC / extruder) and FLY-LLL Plus buffer/safety sensors.
* **Automatic Multi-Language Support (21 Languages):** Automatically detects the active UI locale in both **Mainsail** and **Fluidd** and live-translates the CFS-C panel, controls, and `Map & Print` dialog across all 21 Mainsail/Fluidd languages (`en`, `de`, `fr`, `es`, `it`, `nl`, `pl`, `cs`/`cz`, `sk`, `sl`, `da`, `sv`/`se`, `ca`, `hu`, `pt`, `ru`, `uk`, `tr`, `zh`, `ja`, `ko`).
* **Estimated Remaining Filament Display (RFID Spools):** Displays a color-matched progress bar with remaining percentage (`%`) and estimated weight (`~g`) directly on each slot card whenever a verified RFID spool (`editable: 0` with valid `total_len`) is loaded, while staying cleanly hidden for manual non-RFID spools.
* **Creality-Print-Style `Map & Print` Dialog & Split-Pill Toggle (`k1c-cfs-map.js`):**
  * **Automatic G-Code & Live Slot Matching:** Parses all filaments used in the selected G-Code (material type and slicer color) and auto-assigns them to the closest matching loaded CFS channel (`1A`–`1D`) by RGB color distance and compatible material, independent of slicer tool order.
  * **Missing-Material Print Block:** If a required material is not loaded in any CFS slot, the selector stays unassigned with a warning banner and disables **Apply & Print** until a valid slot is loaded or chosen.
  * **Unified Split-Pill Control (`Map & Print | Open on Print / Only on Mismatch`):** Includes a live mode switch right next to `Map & Print` (automatically pauses when `nexusp` / CFS macros are deactivated via FLY-LLL Plus) and intercepts slicer `Send & Print` uploads before Klipper starts via the bundled lightweight upload gate (`:8088`).
* **Bundled Material Database Editor (`Material DB` Button, Port `8088`):** Automatically installed as a zero-overhead background service (`S99cfs_material_editor`), enabling the native **Material DB** modal button directly inside the CFS-C panel to add, clone, and edit custom `90001+` filament profiles in `material_database.json`. Custom `90001+` profiles automatically bump the database version timestamp so they can be pulled directly over SSH into [CFS RFID (DnG-Crafts/K2-RFID)](https://github.com) to write custom **MIFARE Classic 1K** spool tags.
* **Automatic Non-ASCII / Umlaut Object Name Sanitizer (Adaptive Bed Mesh Fix):** The bundled background service (`server.py`) automatically monitors newly uploaded G-Code files and converts non-ASCII characters (such as German umlauts `ä`, `ö`, `ü`, `ß`, accents, or special symbols) in `EXCLUDE_OBJECT_* NAME=...` lines into clean ASCII (`Würfel` -> `Wuerfel`), working around a Creality `prtouch_v3` parser bug that otherwise causes `CALIBRATEMODE=2` (Adaptive Bed Mesh) to fall back to a full-bed mesh.

> ℹ️ **Fork Note:** This is a fork of the original repository [HimAndRobot/creality-cfs-mainsail-integration](https://github.com).  
> This specific repository includes a critical frontend patch by **@hmsGhostship** to add full compatibility with newer 2025/2026 Creality printer revisions (using `nexusp` and the modern hardware architecture) where the legacy port `9999` socket is missing.

---

This project integrates a simple, compact CFS panel directly inside the **Mainsail** or **Fluidd** interface on the Creality K1C.

<img width="651" height="621" alt="grafik" src="https://github.com/user-attachments/assets/36f38895-818e-42be-9087-155fe1f97e56" />

---

## 🚀 Features

| Category | Supported Functions |
| :--- | :--- |
| **Monitoring** | View all four CFS channels, filament types, humidity levels, and enclosure/spool temperatures |
| **Control** | Trigger `Feed`, `Switch`, and `Retract` actions directly from the web panel |
| **Configuration** | Edit filament details and customize change/purge temperatures for each material type |
| **UI/UX** | Responsive panel loader with built-in button lockouts and loading indicators during printer-side actions |
| **Mobile App** | Includes a mobile application to register multiple printers, manage filaments, and capture spool colors via camera |

---

## 📋 Requirements

Before installing, ensure your printer meets the following prerequisites:
* **Root access** is enabled on the machine.
* **Creality Helper Script** (Guilouz) is installed.
* **Mainsail** and/or **Fluidd** frontend is installed and fully functional.

---

## 🛠️ Installation

Connect to your printer via SSH and execute the following commands:

```sh
git clone https://github.com/hmsGhostship/creality-cfs-mainsail-integration.git /usr/data/creality-cfs-mainsail-integration
cd /usr/data/creality-cfs-mainsail-integration
chmod +x ./menu.sh
./menu.sh
```

**Inside the script menu:**
1. Press `1` for **Install**.
2. Choose your frontend: `Mainsail`, `Fluidd`, or `Both`.
3. Wait for the installation script to finish, then press `Enter`.
4. Refresh your selected web interface in your browser.

---

## ⚠️ Compatibility Note (Creality K1 2025 / K1C 2025)
* **Tested Firmware:** Creality Firmware **`1.1.0.27`** (K1 2025 Series).
* **Architecture Difference:** Unlike older pre-2025 K1 models (which used `/usr/bin/web-server` on port `9999`), 2025 models communicate via **`nexusp` (`CS56nexusp_service`)** and Klipper `box.cfg`—and also work without a CFS-C unit in **Spool Holder Mode**.
* **Recommended Installation:** Use the full [Creality Helper Script 2025 (CFS-C)](https://github.com/hmsGhostship/Creality-Helper-Script-2025) for one-click installation, backup/restore, and hardware sensor integration.


## 🔄 Updates & Removal

### Updating the Integration
To grab the latest features or frontend patches, simply run the script menu again:
```sh
cd /usr/data/creality-cfs-mainsail-integration
./menu.sh
```
1. Press `4` for **Update**.
2. Select your frontend choice (`Mainsail`, `Fluidd`, or `Both`).
3. The script will perform a fast-forward `git pull`, overwrite the injected web assets, and update the Klipper extension files while leaving your current CFS service state untouched.

### Removing the Integration
If you wish to cleanly revert all web interfaces to their original state:
1. Open the script menu and press `2` for **Remove**.
2. The script will safely detach the injected interface code. If option `3` was previously used to enable the legacy Creality CFS socket helper, it will automatically shut it down to preserve system resources.

---

## 📱 Mobile App (Optional)

The repository packages an Expo-based smartphone application located inside the `app/` directory.

**App Features:**
* Add and monitor multiple printers by custom labels and IP addresses.
* Establish direct, real-time WebSocket communication with your printer.
* Select spool colors manually or capture them accurately using your phone's camera.

**Running the App (on your PC / Mac):**

```bash
cd creality-cfs-mainsail-integration/app
npm install
npm start
```

> **WSL2 / Firewall / iOS Tip:** If your phone cannot connect directly over LAN (e.g. when running inside WSL2 NAT), start the server in tunnel mode instead (requires a free Expo account via `npx expo login` on PC and in Expo Go):
> ```bash
> npm run tunnel
> ```
Once the bundler finishes initializing, scan the generated terminal QR code using the **Expo Go** application on your smartphone.

---

*Disclaimer: This software is provided "as is" without warranty of any kind. Use it at your own risk.*
