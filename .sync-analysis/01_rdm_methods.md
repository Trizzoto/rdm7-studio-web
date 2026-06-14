# RDM-7 Web Studio: window.RDM Method Surface

## Connection Management (Lines 1124-1186)

| Method | Signature | Returns | Notes |
|--------|-----------|---------|-------|
| `setMode()` | `setMode(mode, opts)` | void | Sets transport to 'local', 'wifi', 'hotspot', or 'usb'; persists to localStorage |
| `restoreLastConnection()` | `restoreLastConnection()` | void | Loads from localStorage, re-establishes previous transport mode |
| `getConnectionSettings()` | `getConnectionSettings()` | object | Returns {mode, ip?, port?, portName?} |
| `isConnected()` | `isConnected()` | boolean | Returns true if mode !== 'local' |
| `onModeChange()` | `onModeChange(fn)` | void | Registers listener callback |
| `isTauri()` | `isTauri()` | boolean | Checks window.__TAURI_INTERNALS__ |
| `getBaseUrl()` | `getBaseUrl()` | string | Returns transport.baseUrl or empty |
| `tauriInvoke()` | `tauriInvoke(cmd, args)` | Promise<any> | Direct proxy to Tauri backend |

## Device Discovery & Serial (Tauri Only, Lines 1188-1235)

| Method | Signature | Returns | Tauri Command |
|--------|-----------|---------|---------------|
| `discoverDevices()` | `discoverDevices()` | Promise<Array> | 'discover_devices' |
| `listSerialPorts()` | `listSerialPorts()` | Promise<Array> | 'serial_list_ports' |
| `autoDetectDevice()` | `autoDetectDevice()` | Promise<object\|null> | 'serial_auto_detect' |
| `serialConnect()` | `serialConnect(portName)` | Promise<void> | 'serial_connect' + setMode('usb') |
| `serialDisconnect()` | `serialDisconnect()` | Promise<void> | 'serial_disconnect' + setMode('local') |
| `serialIsConnected()` | `serialIsConnected()` | Promise<boolean> | 'serial_is_connected' |

## File Dialogs & I/O (Tauri Only, Lines 1315-1375)

| Method | Signature | Returns | Tauri Command |
|--------|-----------|---------|---------------|
| `saveFileDialog()` | `saveFileDialog(defaultName, filters)` | Promise<string\|null> | 'plugin:dialog\|save' |
| `openFileDialog()` | `openFileDialog(filters)` | Promise<string\|null> | 'plugin:dialog\|open' |
| `writeFile()` | `writeFile(path, data)` | Promise<void> | 'write_binary_file' |
| `readFile()` | `readFile(path)` | Promise<Uint8Array> | 'read_binary_file' |

## Layout Management (Lines 1238-1249)

All async, delegate to this._transport:

- `listLayouts()` → transport.listLayouts()
- `loadLayout(name)` → transport.loadLayout(name)
- `loadCurrentLayout()` → transport.loadCurrentLayout() [USB only]
- `setActiveLayout(name)` → transport.setActiveLayout(name) [USB only]
- `saveLayout(name, data)` → transport.saveLayout(name, data)
- `deleteLayout(name)` → transport.deleteLayout(name)
- `renameLayout(oldName, newName)` → transport.renameLayout(oldName, newName)

## Splash Screens (Lines 1251-1255)

All async, delegate to this._transport:

- `listSplashes()` 
- `loadSplash(name)` 
- `saveSplash(name, data)` 
- `deleteSplash(name)` 
- `renameSplash(oldName, newName)`

## Images (Lines 1257-1262)

All async, delegate to this._transport:

- `listImages()`
- `addImageMeta(meta)`
- `removeImageMeta(name)`
- `getImageData(name)` → base64
- `setImageData(name, b64)` → stores base64
- `deleteImage(name)`

## Fonts (Lines 1264-1269)

All async, delegate to this._transport:

- `listFonts()`
- `addFontMeta(meta)`
- `removeFontMeta(name)`
- `getFontData(name)` → base64
- `setFontData(name, b64)` → stores base64
- `deleteFont(name)`

## Presets (Lines 1271-1272)

- `getPresets()` → Promise<object>
- `savePresets(data)` → Promise<void>

## Storage & System (Lines 1274-1302)

- `getStorageInfo()` → {layouts, images, fonts, totalBytes, maxBytes, sd?}
- `getScreenshot()` → ObjectURL or null
- `getDeviceInfo()` → device info or null
- `getBrightness()` → 0-255 or null
- `setBrightness(val)` → void
- `getCanConfig()` → CAN config or null
- `setCanConfig(cfg)` → void
- `injectSignal(name, value)` → void
- `toggleSimulation(enable)` → status object
- `getSimulationStatus()` → status object
- `getDimmerConfig()` → config or null
- `setDimmerConfig(cfg)` → void
- `getSystemHealth()` → {cpu_temp, mem, uptime} or null
- `reboot()` → void
- `getSignalValues()` → {name: value} or null

## Data Logging (Lines 1289-1294)

- `startLogging()` → void
- `stopLogging()` → void
- `getLogStatus()` → {recording, size} or null
- `listLogs()` → [log_names]
- `downloadLog(name)` → Blob or null (WiFi only)
- `deleteLog(name)` → void

## Fuel Calibration (Lines 1295-1297)

- `getFuelStatus()` → {level, raw, ...} or null
- `setFuelEmpty()` → void
- `setFuelFull()` → void

## WiFi Configuration (Lines 1298-1299)

- `getWifiConfig()` → {ssid, password?, ...} or null
- `setWifiConfig(cfg)` → void

## Device Preview (Lines 1300-1302)

- `applyToDevice(name)` → void (activates layout)
- `previewOnDevice(data)` → void (WiFi only; USB stub at line 1068)
- `testConnection()` → device version or null

## OTA & SD Card (Lines 1304-1310)

- `uploadFirmware(data, onProgress)` → Promise<object>
- `getOtaStatus()` → status or null
- `getSdStatus()` → {inserted, capacity} or null
- `listSdFiles()` → [filenames]
- `copySdFile(src, dst)` → void
- `deleteSdFile(path)` → void

## Bundle (Lines 1312-1313)

- `exportRdmBundle(layout)` → layout (no-op stub)
- `importRdmBundle(data)` → data (no-op stub)

---

**Total Methods:** 75 public methods across 12 categories

**Transport Delegation:** 
- Lines 1237–1313: All actual work delegated to this._transport
- LocalTransport: Default (offline)
- WifiTransport: HTTP to ESP32
- UsbTransport: JSON-RPC over serial (Tauri backend)
