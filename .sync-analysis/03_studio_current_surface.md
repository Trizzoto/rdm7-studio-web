# RDM-7 Studio Current Surface Inventory
**Analysis Date**: 2026-06-10  
**Target File**: `public/index.html` (13,979 lines)  
**Schema Version**: 12 (lines 10987–10999)  

---

## 1. WIDGET_DEFS: Defined Types & Fields

**Status**: PRESENT (COMPLETE) — All 15 widget types match firmware enumeration.

### Defined Widget Types (lines 5546–5955)

| Widget Type | Display Name | Default Size | Field Count | Key Fields |
|---|---|---|---|---|
| `rpm_bar` | RPM Bar | 800×55 | 7 | rpm_max, redline, bar_color, limiter_effect, limiter_value, limiter_color |
| `panel` | Panel | 155×92 | 22 | label, decimals, warning_high/low (enabled, threshold, color, apply_label/value/panel), border_radius, border_width, bg_color, bg_opa |
| `bar` | Bar Graph | 300×30 | 26 | bar_min, bar_max, show_bar_value, anchor_enabled/value/position, invert_bar_value, bar_in_range_color, bar_alerts_enabled, bar_low/high (threshold, color) |
| `indicator` | Turn Indicator | 50×50 | 8 | slot (L/R), input_source (Wire/CAN), animation, is_momentary, color_on, opa_on, color_off, opa_off |
| `warning` | Alert Light | 25×25 | 12 | label, image_name, active_color, active_opa, is_momentary, invert_toggle, inactive_color, inactive_opa, border_width, radius, show_label, label_color |
| `text` | Text / Value | 100×30 | 5 | static_text, decimals, rotation, font, text_color |
| `meter` | Meter | 300×300 | 41 | min, max, start_angle_user, sweep_degrees, reverse, minor/major_tick_step, anchor_enabled/value/position, redline_enabled (threshold, color, show_arc, arc_width, arc_r_mod, recolor_ticks), meter_bg_color, border_color, show_ticks, minor/major_tick_width/length/color, show_tick_labels, tick_label_font/color/divisor, needle_image_name, needle_pivot_x/y, needle_angle_offset, needle_width, needle_color, needle_r_mod, needle_rear_length, needle_tip_style, needle_tip_base_w/point_w/taper (with autoFn), needle_ball_size/color |
| `image` | Image | 100×100 | 6 | image_name, auto_size, image_scale, opacity, recolor, recolor_opa |
| `shape_panel` | Shape Panel | 200×100 | 11 | shape_type (rectangle/circle/trapezoid/triangle/diamond/arrow_right/arrow_left/chevron_right/chevron_left), taper, taper_side, bg_color, bg_opa, border_color, border_width, border_radius, shadow_width, shadow_color, shadow_opa, shadow_ofs_x/y |
| `line` | Line | 200×4 | 6 | line_color, line_width, line_opa, rounded, orientation (horizontal/vertical/diagonal_fwd/diagonal_bwd), dash_gap |
| `arc` | Arc Shape | 200×200 | 23 | start_angle, end_angle, signal_min, signal_max, arc_width, arc_color, bg_arc_color, bg_arc_width, rounded_ends, arc_image, arc_image_full, redline_enabled (threshold, color, arc_width, recolor_fill), limiter_effect (0=None/1=Flash/2=Solid), limiter_value, limiter_color, flash_speed_ms, show_value, value_font, value_color, value_y_offset, value_decimals, value_unit |
| `toggle` | Toggle Switch | 80×40 | 18 | label, show_label, signal_on_threshold, tx_can_id, tx_bit_start, tx_bit_length, tx_endian, tx_rate_hz, momentary, image_name, active_color, inactive_color, active_opa, inactive_opa, label_color, font, label_align, label_x, label_y |
| `button` | Button | 100×40 | 18 | label, show_label, latch, tx_can_id, tx_bit_start, tx_bit_length, tx_endian, tx_rate_hz, tx_send_release, image_name, pressed_image_name, bg_color, text_color, pressed_color, border_radius, font, label_align, label_x, label_y |
| `shift_light` | Shift Light | 800×30 | 16 | signal_name, led_count, range_min, range_max, flash_threshold, flash_speed, color_low, color_mid, color_high, color_off, led_spacing, border_radius, led_width, led_height, fill_mode (0=L-to-R/1=Outside-In), threshold_mid, threshold_high |

**Total**: 15 widget types, 205 unique field definitions across all types.

### Recent Custom Fields Present

- **Arc**: show_value, value_font, value_color, value_y_offset, value_decimals, value_unit (CENTER VALUE TEXT)
- **Arc**: redline_enabled, redline_threshold, redline_color, redline_arc_width, redline_recolor_fill (REDLINE ZONE)
- **Arc**: limiter_effect, limiter_value, limiter_color, flash_speed_ms (LIMITER FLASH/SOLID)
- **Meter**: show_ticks, minor/major_tick_width/length/color (TICK CUSTOMIZATION)
- **Meter**: show_tick_labels, tick_label_font, tick_label_color, tick_label_divisor (LABEL CONTROL)
- **Meter**: redline_enabled, redline_threshold, redline_color, redline_arc_width, redline_recolor_ticks (REDLINE)
- **Meter**: needle_tip_style with autoFn (STYLE-SPECIFIC AUTO DEFAULTS)
- **Bar**: anchor_enabled, anchor_value, anchor_position (NON-LINEAR CURVE)
- **Bar**: bar_alerts_enabled (CONDITIONAL THRESHOLDS)
- **Panel**: warning_high/low_apply_label/value/panel (ALERT APPLY-FLAGS)
- **RPM Bar**: limiter_effect, limiter_value, limiter_color (LIMITER)
- **Shift Light**: threshold_mid, threshold_high (2-LEVEL), fill_mode (L-to-R/Outside-In)

**Status**: PRESENT — All post-v10 enhancements in WIDGET_DEFS.

---

## 2. buildFirmwarePayload() & Schema Conversion (lines 7503–7605)

**Status**: PRESENT (CURRENT)

### Key Processing Steps

| Step | Lines | Description |
|---|---|---|
| Deep clone layout | 7504 | `JSON.parse(JSON.stringify(currentLayout))` |
| Filter hidden widgets | 7510–7512 | Removes widgets with `w.hidden === true` |
| Screen dimensions | 7514–7515 | Adds `payload.screen_w`, `payload.screen_h` |
| RGB888→RGB565 | 7518 | `convertWidgetColors(payload.widgets, rgb888to565)` BEFORE rule conversion |
| Signal binding | 7521–7530 | Maps `w.signal` → `w.config.signal_name` |
| Bar alert coercion | 7532–7539 | If `!bar_alerts_enabled`, sets thresholds to min/max to prevent triggers |
| Image scale conversion | 7542–7544 | UI % (10–200) → LVGL zoom (256=100%) |
| Meter tick logic | 7547–7559 | Disables ticks by zeroing lengths; hides labels by setting major_tick_every > count |
| Cleanup web-only fields | 7561 | Deletes `bar_alerts_enabled` |
| Rule override array conversion | 7563–7584 | Object `{fieldName: value}` → array `[{field, type, value}]` with type inference |
| Endian conversion | 7586–7594 | `is_little_endian` (bool) → `endian` (0/1) |
| Prune unused signals | 7596–7603 | Removes signals not bound to any widget |

### Schema Version

- **CURRENT_SCHEMA = 12** (line 10987)
- **Splash creation**: schema_version: 10 (line 6289)
- **Import/export**: Supports migration v1–v12+5

### Color Conversion (lines 5326–5350)

**Status**: PRESENT (BIDIRECTIONAL RGB565↔888)

```javascript
rgb565to888(val)           // Expand 5-6-5 bits to 8-8-8 with replication
rgb888to565(val)           // Pack 8-8-8 to 5-6-5 by dropping LSBs
snapToRgb565(val)          // Round to nearest representable RGB565
convertWidgetColors(widgets, convertFn)  // Apply to all color fields + rule overrides
```

**Integration**: Called BEFORE rule override array conversion (line 7518).

---

## 3. CHANNELS System

**Status**: ABSENT — Studio uses **signals[]-only** (no channels layer).

### What Exists

- **Signals array**: `currentLayout.signals[]` with CAN metadata (can_id, bit_start, bit_length, endian, scale, offset, decimals)
- **Widget signal binding**: `w.signal` or `w.config.signal_name`
- **ECU Presets** (lines 4352–4363): Hardcoded list of ECU+version → channel mappings
  - Each: `{ecu, version, label, can_id, endianess, bit_start, bit_length, scale, offset, decimals, is_signed}`
  - Populates signals when user selects ECU context
  - **No persistent channels table**

### Missing vs Firmware

| Feature | Firmware | Studio |
|---|---|---|
| Channels list UI | ✓ | ✗ ABSENT |
| Channel-to-signal mapping | ✓ | ✗ Only signals[] |
| Per-channel threshold presets | ✓ | ✗ ABSENT |
| Channel source badge (CAN/wire) | ✓ | ✗ ABSENT |
| DBC per-channel import | ✓ | ✗ Manual ECU select only |

---

## 4. Signal Manager / ECU Preset Browser / DBC Import

**Status**: PRESENT (FUNCTIONAL)

### Signal Manager Modal (lines 1439–1790, 2596–2665)

- **Trigger**: `openSignalManager()` (line 9371) via Signals tab or `S` key
- **Modes**: Manual creation, ECU preset picker
- **Features**: Add signals with CAN fields, browse presets, filter by ECU context, DBC import

### DBC Import Modal (lines 1750–1790, 2671–2698)

- **Trigger**: Button in Signal Manager (line 2765)
- **Features**: File upload, preview, ECU name/version metadata, converts DBC → signals[]
- **Status**: FUNCTIONAL

### ECU Preset Creation (lines 4460, 8585–8744)

- **Status**: PRESENT
- **Mechanism**: Custom ECU preset form; signals added to `currentLayout.signals[]`

---

## 5. Rules System (Widget Conditional Overrides)

**Status**: PRESENT (FULL SUPPORT)

### Structure (lines 8020–8542)

```javascript
w.config.rules = [{
    signal_name: 'engine_rpm',
    op: '>',  // '>', '<', '>=', '<=', '==', '!='
    threshold: 6500,
    overrides: {
        // Web editor object format → Firmware array format conversion
        bar_color: 0xFF0000
    }
}]
```

### Implementation

- **Add rule**: `addRule()` (line 8520)
- **Delete rule**: `deleteRule()` (line 8527)
- **Update rule**: `updateRuleField()`, `updateRuleOverride()` (lines 8531–8542)
- **Conversion**: Object `{fieldName: value}` → array `[{field, type, value}]` in buildFirmwarePayload (lines 7563–7584)
- **Type inference**: Checks WIDGET_DEFS (color/bool/number/string)
- **Widget support**: All 15 types

**Status**: PRESENT (COMPREHENSIVE)

---

## 6. Per-Widget Recent Customizations

### Arc (lines 5828–5883)

- **Redline zone**: redline_enabled, threshold, color, arc_width, recolor_fill ✓ PRESENT
- **Limiter**: limiter_effect (0=None/1=Flash/2=Solid), value, color, flash_speed_ms ✓ PRESENT
- **Center value text**: show_value, value_font, value_color, value_y_offset, value_decimals, value_unit ✓ PRESENT
- **Missing**: ticks_outside, minor_ticks, major_ticks, value_line, custom_ticks ✗ NOT IN WIDGET_DEFS

### Meter (lines 5670–5762)

- **Ticks**: show_ticks, minor/major_tick_width/length/color ✓ PRESENT
- **Tick labels**: show_tick_labels, tick_label_font/color, tick_label_divisor ✓ PRESENT
- **Redline zone**: redline_enabled, threshold, color, arc_width, recolor_ticks ✓ PRESENT
- **Needle tip auto-tuning**: needle_tip_style with autoFn ✓ PRESENT
- **Missing**: ticks_outside ✗ NOT IN WIDGET_DEFS

### Bar (lines 5595–5627)

- **Anchor curve**: anchor_enabled, anchor_value, anchor_position ✓ PRESENT
- **Alerts**: bar_alerts_enabled, bar_low/high, colors ✓ PRESENT
- **Missing**: center_fill ✗ NOT IN WIDGET_DEFS

### Panel (lines 5562–5593)

- **Alert apply-flags**: warning_high/low_apply_label/value/panel ✓ PRESENT (3+3 fields)
- **Status**: COMPREHENSIVE

### RPM Bar (lines 5547–5561)

- **Limiter**: limiter_effect (0–6), value, color ✓ PRESENT

### Shift Light (lines 5933–5954)

- **3-color scheme**: color_low, color_mid, color_high, color_off ✓ PRESENT
- **Fill mode**: fill_mode (L-to-R / Outside-In) ✓ PRESENT
- **2-level thresholds**: threshold_mid, threshold_high ✓ PRESENT

---

## 7. Wideband & Stale Indicator Support

**Status**: PRESENT (BASIC STALE DISPLAY ONLY)

### Stale Signal Handling (lines 9528–9543, 12449–12452)

- **Live value tracking**: `_signalLiveValues = {name → {value, stale}}`
- **UI display**: 
  - Stale signals: `---` (gray text, line 9542)
  - Live signals: colored (cyan #4fc3f7, line 9543)
- **Status indicator**: Green dot (live) / Red dot (stale) in signals table (line 12452)

**Status**: PRESENT-BUT-STALE  
*Gap*: No wideband-specific UI (AFR sensor, lambda, voltage). Binary stale display only.

---

## 8. Splash Screen Support (lines 2218–6341)

**Status**: PRESENT (FUNCTIONAL)

- **Allowed widget types**: image, text, shape_panel (line 4085)
- **Management**: Create, duplicate, delete, switch (lines 6287–6340)
- **Schema**: Splash payloads use schema_version: 10 (line 6289)
- **UI mode**: Toggle "Splash Screen" vs "Dashboard" (lines 6181–6216)

---

## 9. Gap Summary: Studio vs Firmware Structure

### Present & Current

✓ WIDGET_DEFS (15 types, 205 fields) — Matches firmware  
✓ buildFirmwarePayload() — Schema v12, RGB565, rule array conversion  
✓ Color conversion — Bidirectional RGB565↔888  
✓ Signal Manager — Manual + ECU preset + DBC import  
✓ Rules system — Conditional per-widget  
✓ Per-widget customizations — Arc/Meter redline/limiter, center value, tick control, anchor curves, alert apply-flags  
✓ Shift Light — 3-color + fill modes + 2-level thresholds  
✓ Splash screens — Full lifecycle  
✓ Stale indicator — Basic display  

### Absent or Stale

✗ CHANNELS — No channels layer; signals[] only  
✗ Arc: ticks_outside, minor_ticks, major_ticks, value_line, custom_ticks  
✗ Bar: center_fill  
✗ Meter: ticks_outside  
✗ Wideband-specific UI — AFR/lambda/sensor-health  
✗ Per-channel threshold presets — No channels table  
✗ DBC per-channel import — Manual ECU select only  

---

## 10. Gap Table

| Subsystem | Studio State | Firmware | Gap |
|---|---|---|---|
| Widget Types | 15/15 ✓ | 15 | NONE |
| Widget Fields | 205 core ✓ | ~210+ | ~5 missing: arc ticks/value_line/custom, bar center_fill, meter ticks_outside |
| Schema Version | v12 ✓ | v12 | NONE |
| Color Conversion | RGB565↔888 ✓ | RGB565 | NONE |
| Channels System | ABSENT | PRESENT | MAJOR — No channels UI/thresholds |
| Signal Manager | Manual + Presets + DBC ✓ | N/A | MINOR — DBC limited |
| Rules / Conditionals | Full ✓ | v12+ | NONE |
| Redline / Limiter | Arc + Meter + RPM ✓ | All | COMPLETE |
| Tick Control | Meter ✓ | Arc + Meter | Arc missing ticks/value_line/custom |
| Center Value (Arc) | ✓ | ✓ | NONE |
| Anchor Curves | Bar + Meter ✓ | Bar + Meter | NONE |
| Stale Indicator | Basic "---" | ? | PARTIAL — No sensor health |
| Splash Screens | Full ✓ | Via layouts | NONE |
