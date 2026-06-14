# RDM-7 Web Studio Architecture Analysis

## Files in This Report

1. **01_rdm_methods.md** - Complete window.RDM method surface (75 methods)
2. **02_transports.md** - Backend implementations (Local, WiFi, USB transports)
3. **03_gaps.txt** - Firmware API gaps (42 unmapped endpoints)

## Quick Reference

### RDM Method Surface (75 total)

- **Connection Management** (8 methods) — setMode, serialConnect, etc.
- **Device Discovery** (6 methods, Tauri-only) — discoverDevices, listSerialPorts
- **File Dialogs** (4 methods, Tauri-only) — saveFileDialog, openFileDialog
- **Layout Management** (7 methods) — listLayouts, loadLayout, saveLayout, etc.
- **Splash Screens** (5 methods) — listSplashes, saveSplash
- **Images** (6 methods) — listImages, getImageData, setImageData
- **Fonts** (6 methods) — listFonts, getFontData, setFontData
- **Presets** (2 methods) — getPresets, savePresets
- **Storage & System** (15 methods) — getDeviceInfo, getBrightness, getSystemHealth
- **Signals & Simulation** (4 methods) — getSignalValues, injectSignal, toggleSimulation
- **Logging** (6 methods) — startLogging, getLogStatus, listLogs
- **Fuel, WiFi, OTA, SD** (7 methods) — getFuelStatus, uploadFirmware
- **Bundle** (2 no-op stubs) — exportRdmBundle, importRdmBundle

### Transport Implementations

| Transport | Offline | Network | Medium | Data |
|-----------|---------|---------|--------|------|
| LocalTransport | Yes | No | localStorage + IndexedDB | Full layouts + metadata |
| WifiTransport | No | Yes | HTTP fetch | /api/* endpoints |
| HotspotTransport | No | Yes | HTTP (hardcoded 192.168.4.1) | /api/* endpoints |
| UsbTransport | No | Yes (serial) | JSON-RPC via Tauri | RPC methods |

### LocalTransport Storage

**localStorage:**
- rdm7_layout_<name>, rdm7_layout__splash_<name> — full JSON layouts
- rdm7_images, rdm7_fonts — metadata arrays
- rdm7_custom_presets — preset dictionary
- rdm7_connection_settings — last connection state

**IndexedDB (rdm7_desktop_db):**
- image_data, font_data — base64 binary (preferred over localStorage)

**Quota:** 5 MiB max

### WifiTransport API Coverage

~40 mapped endpoints:
- /api/layout/* — layout CRUD
- /api/splash/* — splash management
- /api/image/* / /api/font/* — asset management
- /api/device/info, /api/brightness, /api/can/config — system config
- /api/signal/inject, /api/signal/simulate — signal testing
- /api/log/* — logging CRUD
- /api/fuel/* — fuel calibration
- /api/wifi/config — WiFi settings
- /api/ota/upload — firmware upload
- /api/sd/* — SD card file management

### UsbTransport RPC Methods

Similar to WifiTransport but JSON-RPC over serial (Tauri backend).
Uses 'serial_request', 'serial_download_base64', 'serial_upload_chunked' commands.
Presets fallback to LocalTransport. Screenshot returns null (binary not exposed).

---

## WASM Preview Engine

### Loading (index.html)

Script: build/index.js → LVGLPreview() factory

### Functions Called

| WASM Function | Purpose | Input |
|---------------|---------|-------|
| set_display_size(w, h) | Resize preview | width, height (default 800×480) |
| register_image(name, w, h, ptr) | Load image | name + WASM heap pointer |
| register_font(name, ptr) | Load font | name + WASM heap pointer |
| load_layout_json(json) | Render layout | Full layout JSON string |
| inject_signal(name, value) | Test signal | Signal name + numeric value |

### Memory Pattern

1. RDM.getImageData(name) → base64
2. Convert to Uint8Array
3. _malloc(size) in WASM
4. HEAPU8.set(data, ptr)
5. ccall('register_image', null, types, [name, w, h, ptr])
6. _free(ptr)

---

## Default Layout Schema

**Version:** 11
**Fields:**
- schema_version, name, screen_w (800), screen_h (480)
- ecu: "MaxxECU", ecu_version: "1.3"
- widgets: [25 items] — panels, bars, text, images, indicators, warnings
- signals: [13 items] — CAN-based signal definitions

**Signal Model:** Pure CAN J1939/CANdb++ (bit-offset based decoding)

---

## Critical Gaps (42 Unmapped Endpoints, 51%)

1. **Splash Control** (4) — /api/splash/set, fade, enabled, bootanim
2. **ECU Selection** (4) — /api/ecu/* (multi-ECU support unreachable)
3. **Channels** (7) — /api/channels/* (possible signals replacement)
4. **OBD2** (8) — /api/obd2/* (full diagnostics unreachable)
5. **Advanced Logging** (8) — /api/log/config, /api/canraw/*
6. **Advanced OTA** (2) — /api/ota/check, /api/ota/start
7. **Vehicle Config** (2) — /api/gear/config, /api/odometer
8. **Test/Debug** (5) — /api/banner/test, /api/indicator/test, etc.
9. **Signal Mgmt** (2) — /api/signal/clear, /api/signal/update
10. **Presets** (3) — /api/presets/custom/*
11. **Streaming** (1) — /api/capture/stream (MJPEG)

---

## Key Observations

### Schema & Data Model Risk

The **default layout uses signals[] (CAN-only model)**, but firmware has **/api/channels/** endpoints suggesting a newer multi-source signal architecture. Future firmware may deprecate signals without backward-compatible migration.

### ECU Coupling

Layouts are hardcoded to specific ECU types (e.g., "MaxxECU"). Unclear how multi-ECU setups work or if layouts are portable between ECU types.

### Signal Injection for Testing

`injectSignal(name, value)` and `toggleSimulation(enable)` work, but `/api/signal/clear` and `/api/signal/update` endpoints are unreachable.

### OBD2 Diagnostics

Firmware exposes full OBD2 stack (/api/obd2/*), but studio has **zero methods** for VIN, DTC retrieval, protocol negotiation, or PID testing.

### Logging & Cloud Sync

Advanced logging features (/api/log/config, /api/canraw/*, cloud_upload) are unreachable. Studio can only start/stop/list logs, not configure what's logged.

---

## Source Files

- **transport.js** (1201 lines)
  - Default layout: lines 11–57
  - LocalTransport: lines 137–353
  - WifiTransport: lines 359–802
  - UsbTransport: lines 809–1100
  - RDM global object: lines 1118–1376

- **index.html** (partial)
  - WASM module: line 2575 (build/index.js)
  - Module init: line 3885
  - Image/font registration: lines 3934–4013
  - Layout loading: line 4030

- **Firmware** (main/net/)
  - web_server_layout.c — layout endpoints
  - web_server_assets.c — image/font endpoints
  - web_server_system.c — device config endpoints
  - web_server_signals.c — signal/simulation endpoints
  - web_server_channels.c — new channels architecture (unmapped)
  - web_server_obd2.c — OBD2 integration (unmapped)

