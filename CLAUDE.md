# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**rdm7-studio-web** is a web-hosted dashboard designer for the RDM-7 system. It's a static SPA deployed on Vercel that lets users design vehicle dashboard displays online.

This repo is one part of the broader RDM-7 project, which spans four repositories:
- **RDM Studio Web** (this repo) — browser-based dashboard designer
- **RDM Studio Desktop** — Tauri-based desktop app with USB/serial device connectivity
- **RDM Marketplace** — community marketplace for sharing dashboard layouts
- **RDM-7 Dash** — the embedded dashboard firmware running on the physical device

## Commands

```bash
# Start local dev server (static file serving on port 3002)
npm run dev

# No build step — this is a static site with pre-compiled WASM
npm run build  # just echoes "no build needed"
```

There are no tests, linters, or formatters configured.

## Architecture

### Static SPA — No Build Pipeline

All source is committed directly. There is no bundler, transpiler, or framework. The app is served as static files from `public/`.

### Key Files

- **`public/index.html`** — The entire application: ~11K lines of embedded HTML + CSS + JS. This is the monolithic SPA containing all UI logic, event handling, and state management.
- **`public/build/index.js` + `index.wasm`** — Emscripten-compiled LVGL rendering engine. Renders dashboard widget previews onto `<canvas>` (800×480). These are pre-compiled binaries — do not edit.
- **`public/transport.js`** — Transport abstraction layer exposing `window.RDM` API. Implements pluggable backends: `LocalTransport` (LocalStorage, web default), `WifiTransport`, `HotspotTransport`, `UsbTransport`, `TauriTransport`.
- **`public/rdm_logo_data.js`** — Base64-encoded logo binary data.

### How It Works

1. WASM module initializes and renders widgets to canvas via LVGL
2. `transport.js` provides `window.RDM` for storage/device I/O (web uses LocalStorage)
3. Inline JS in `index.html` manages the editor: widget palette, properties panel, layers, signal binding, drag/drop, pan/zoom
4. Signals follow CAN bus structure: `{ can_id, bit_start, bit_length, scale, offset, ... }`
5. Widgets bind to signals via `signal_name` in their config

### UI Structure

- **Canvas container** — bezel-framed display with transform-based pan/zoom
- **Right sidebar** — three tabs: Widgets palette, Properties inspector, Layers panel
- **Header toolbar** — toggle pills, ECU selector, view controls
- **Modals** — Signal Manager, ECU Preset Browser, DBC Import, Assign Data Source

### Web vs Desktop

The web version hides USB/Serial features and shows a preview-only banner. Desktop features (file dialogs, serial ports, auto-detect) are gated behind Tauri availability checks in `transport.js`.

### Mobile / Responsive

Three breakpoints: ≤600px (burger menu), 601–900px (compact), >900px (full). Touch: single-finger select, two-finger pan/zoom. Sidebar FAB for mobile widget access.

## Deployment

Vercel static deployment. `vercel.json` sets COEP/COOP headers on `/build/*` for WASM SharedArrayBuffer support. Output directory is `public/`.

## Important Patterns

- All UI state lives in JS variables in the inline `<script>` block — no state management library
- Widget types: `rpm_bar`, `panel`, `bar`, `indicator`, `warning`, `text`, `image`
- CSS uses a dark Photoshop-style design system (`--bg-sidebar: #303030`, `--accent: #2d8ceb`)
- Google Fonts loaded externally: Sora (UI) and JetBrains Mono (code)
