# RDM-7 Studio ↔ Firmware Gap Summary

## What's in Sync (✓)

**Schema & Serialization**: Studio emits schema v12 matching firmware. Color conversion (RGB565↔888) is bidirectional and applied before all firmware sends. The `buildFirmwarePayload()` function correctly transforms web editor state (object-format rules, web-only flags like `bar_alerts_enabled`) into firmware format (array-format rules with type inference, thresholds coerced to prevent false triggers).

**Widget Types**: All 15 types (rpm_bar, panel, bar, indicator, warning, text, meter, image, shape_panel, line, arc, toggle, button, shift_light) are defined in WIDGET_DEFS (205+ fields). Recent enhancements are present: arc center-value text, arc/meter redline zones with recolor flags, arc/meter/rpm limiter effects with flash modes, meter tick-label customization with divisor scaling, bar/meter anchor-based non-linear curves, panel alert apply-flags (label/value/panel), shift-light 3-color scheme with fill-direction and 2-level thresholds.

**Signal Manager & Rules**: Full manual signal creation, ECU preset browser (MaxxECU, Fury, Link, AIM, etc.), DBC import, and rule-based conditional widget overrides (per-widget, all 15 types supported). Rules correctly convert from web object format to firmware array format with type inference (color/bool/number/string).

**Splash Screens**: Complete lifecycle (create/edit/delete) with restricted widget types (image, text, shape_panel). Separate from dashboard layout system.

---

## What's Missing or Stale (✗)

**Channels System (MAJOR GAP)**: Firmware has a channels layer (list of channel objects with per-channel thresholds, source badges, CAN decode metadata). Studio has **no channels UI**—only a flat `signals[]` array bound directly to widgets. No per-channel threshold presets, no channel source badge, no channel-scoped DBC import. ECU presets exist but are hardcoded lookups, not persistent channels.

**Arc Widget Fields**: Missing `ticks_outside`, `minor_ticks`, `major_ticks`, `value_line`, and `custom_ticks[]`. Studio cannot author arc gauge tick customization or value-line overlay beyond basic start/end angles.

**Bar & Meter Fields**: Bar missing `center_fill` (fill from center outward). Meter missing `ticks_outside` (draws ticks outside the gauge boundary).

**Wideband Support**: Only basic stale indicator (displays `---` in gray for missing signals). No AFR sensor UI, no lambda cross-count, no voltage display, no multi-level signal health (just binary stale/live).

**DBC Import Scope**: Limited to manual ECU selection + file upload. No auto-mapping of DBC signals to channels, no schema validation, no signal re-use across layouts.

---

## Priority Fixes

1. **Add missing arc/bar/meter fields** to WIDGET_DEFS (5 fields, low effort)
2. **Stub channels layer** (show/hide toggle for channel UI in Signal Manager, even if signals[] remains source of truth)
3. **Wideband sensor preset** in ECU presets or Signal Manager
4. **DBC channel mapping** UI (optional; current manual ECU selection works for most cases)

---

**Overall**: Studio is ~95% current with firmware widget authoring, but 0% current with channels/multi-level signal management. The gap is structural (layers), not field coverage.
