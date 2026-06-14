# RDM-7 Transport Implementations

## LocalTransport (Lines 137-353)

### Storage Keys (localStorage)

- rdm7_layout_<name> → layout JSON
- rdm7_layout__splash_<name> → splash JSON
- rdm7_images → metadata array
- rdm7_image_data_<name> → legacy base64 (deprecated)
- rdm7_fonts → metadata array
- rdm7_font_data_<name> → legacy base64 (deprecated)
- rdm7_custom_presets → preset dict
- rdm7_connection_settings → {mode, ip?, port?, portName?}

### IndexedDB Storage

Database: "rdm7_desktop_db" (version 1)
Stores:
- image_data (key: name, value: base64)
- font_data (key: name, value: base64)

### Implemented Methods

Layout (141-171): listLayouts, loadLayout, saveLayout, deleteLayout, renameLayout
Splash (174-200): listSplashes, loadSplash, saveSplash, deleteSplash, renameSplash
Images (203-238): listImages, addImageMeta, removeImageMeta, getImageData, setImageData, deleteImage
Fonts (241-275): listFonts, addFontMeta, removeFontMeta, getFontData, setFontData, deleteFont
Presets (278-285): getPresets, savePresets
Storage (288-308): getStorageInfo (max 5 MiB)
System (311-352): All return null/empty/noop for device features

---

## WifiTransport (Lines 359-802)

### HTTP Transport

Tauri: uses 'http_fetch' and 'http_fetch_binary' commands
Browser: native fetch() with 10-30s timeouts

### API Endpoints

Layouts:
- GET /api/layout/list → listLayouts()
- GET /api/layout/raw?name=<name> → loadLayout(name)
- POST /api/layout/save → saveLayout(name, data)
- POST /api/layout/delete → deleteLayout(name)
- POST /api/layout/rename → renameLayout(oldName, newName)

Splashes:
- GET /api/splash/list → listSplashes()
- GET /api/layout/raw?name=_splash_<name> → loadSplash(name)
- POST /api/layout/save → saveSplash(name, data)
- POST /api/splash/delete → deleteSplash(name)

Images:
- GET /api/image/list → listImages()
- GET /api/image/data?name=<name> → getImageData(name)
- POST /api/image/upload?name=<name> → setImageData(name, b64)
- POST /api/image/delete?name=<name> → deleteImage(name)

Fonts:
- GET /api/font/list → listFonts()
- GET /api/font/data?name=<name> → getFontData(name)
- POST /api/font/upload?name=<name> → setFontData(name, b64)
- POST /api/font/delete?name=<name> → deleteFont(name)

System & Storage:
- GET /api/device/info → getDeviceInfo()
- GET /api/brightness → getBrightness()
- POST /api/brightness → setBrightness(val)
- GET /api/can/config → getCanConfig()
- POST /api/can/config → setCanConfig(cfg)
- GET /api/system/health → getSystemHealth()
- POST /api/system/reboot → reboot()
- GET /api/signals/values → getSignalValues()
- GET /api/storage/info → aggregated storage
- GET /screenshot → getScreenshot() [legacy]

Signals & Simulation:
- POST /api/signal/inject → injectSignal(name, value)
- GET/POST /api/signal/simulate → toggleSimulation() / getSimulationStatus()

Dimmer:
- GET /api/dimmer/config → getDimmerConfig()
- POST /api/dimmer/config → setDimmerConfig(cfg)

Logging:
- POST /api/log/start → startLogging()
- POST /api/log/stop → stopLogging()
- GET /api/log/status → getLogStatus()
- GET /api/log/list → listLogs()
- GET /api/log/download → downloadLog(name)
- POST /api/log/delete → deleteLog(name)

Fuel:
- GET /api/fuel/status → getFuelStatus()
- POST /api/fuel/set-empty → setFuelEmpty()
- POST /api/fuel/set-full → setFuelFull()

WiFi:
- GET /api/wifi/config → getWifiConfig()
- POST /api/wifi/config → setWifiConfig(cfg)

Device Control:
- POST /api/layout/set → applyToDevice(name)
- POST /api/layout/preview → previewOnDevice(data)
- GET /api/layout/version → testConnection()

OTA & SD:
- POST /api/ota/upload → uploadFirmware(data, onProgress) [XHR multipart]
- GET /api/ota/status → getOtaStatus()
- GET /api/sd/status → getSdStatus()
- GET /api/sd/files → listSdFiles()
- POST /api/sd/copy → copySdFile(src, dst)
- POST /api/sd/delete → deleteSdFile(path)

---

## HotspotTransport

Alias: createWifiTransport('http://192.168.4.1')
Identical to WifiTransport, hardcoded baseUrl

---

## UsbTransport (Lines 809-1100)

### JSON-RPC Over Serial (Tauri Backend)

Invocation: _tauriInvoke('serial_request', {method, params})
Response: {result: value} or {error: message}

### RPC Methods

Layouts:
- layout.list → listLayouts()
- layout.raw {name} → loadLayout(name)
- layout.current → loadCurrentLayout()
- layout.set {name} → setActiveLayout(name)
- layout.save {name, data} → saveLayout(name, data)
- layout.delete {name} → deleteLayout(name)

Splashes:
- splash.list → listSplashes()
- layout.raw {name: '_splash_<name>'} → loadSplash(name)
- layout.save {name: '_splash_<name>', data} → saveSplash(name, data)
- layout.delete {name: '_splash_<name>'} → deleteSplash(name)

Images (Tauri-backed):
- image.list → listImages()
- serial_download_base64 {downloadType: 'image', name} → getImageData(name)
- serial_upload_chunked {uploadType: 'image', name, data: [bytes]} → setImageData(name, b64)
- image.delete {name} → deleteImage(name)

Fonts (Tauri-backed):
- font.list → listFonts()
- serial_download_base64 {downloadType: 'font', name} → getFontData(name)
- serial_upload_chunked {uploadType: 'font', name, data: [bytes]} → setFontData(name, b64)
- font.delete {name} → deleteFont(name)

System:
- device.info → getDeviceInfo(), testConnection()
- brightness.get / brightness.set → getBrightness() / setBrightness(val)
- can.config.get / can.config.set → getCanConfig() / setCanConfig(cfg)
- signal.inject {name, value} → injectSignal(name, value)
- signal.simulate {enable} → toggleSimulation(enable) / getSimulationStatus()
- dimmer.get / dimmer.set → getDimmerConfig() / setDimmerConfig(cfg)
- system.health → getSystemHealth()
- system.reboot → reboot()
- signal.values → getSignalValues()
- storage.info / layout.list / image.list / font.list → getStorageInfo()

Logging:
- log.start → startLogging()
- log.stop → stopLogging()
- log.status → getLogStatus()
- log.list → listLogs()
- log.delete {name} → deleteLog(name)
- downloadLog() → null (WiFi recommended)

Fuel:
- fuel.status → getFuelStatus()
- fuel.set-empty → setFuelEmpty()
- fuel.set-full → setFuelFull()

WiFi:
- wifi.config.get → getWifiConfig()
- wifi.config.set → setWifiConfig(cfg)

Device Control:
- layout.set {name} → applyToDevice(name)
- previewOnDevice() → not implemented (stub)
- device.info → testConnection()

OTA (Tauri-backed):
- serial_upload_chunked {uploadType: 'ota', name: 'firmware', data: [bytes]} → uploadFirmware(data, onProgress)
- getOtaStatus() → null

SD Card:
- All return null/[] or noop

Presets:
- getPresets() → delegates to LocalTransport
- savePresets() → delegates to LocalTransport

