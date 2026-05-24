# Agents

List of agents and project components.

## Project Description

This is a **ZMK (Zephyr Mechanical Keyboard)** firmware configuration for a custom split mechanical keyboard called **Fuji44**.

### Overview

Fuji44 is a 44-key split mechanical keyboard with wireless (BLE) connectivity, designed for efficient typing with custom layouts and advanced ZMK features.

### Hardware Specifications

- **Number of keys**: 44 (22 per half)
- **Matrix**: 4 rows × 12 columns (with a gap in the center for ergonomic split design)
- **Platform**: nice_nano (nRF52840) with Bluetooth Low Energy support
- **Connection type**: Wireless (BLE) between halves and host
- **GPIO Configuration**:
  - **Rows**: 4 rows using pins 15, 14, 16, 10 (pro_micro pinout)
  - **Columns**: 6 columns per half (left: pins 4,5,6,7,8,9; right: pins 9,8,7,6,5,4)
  - **Diode direction**: col2row

### Software Configuration

#### Build System
- **Build configuration**: `build.yaml` defines builds for:
  - `fuji44_left` shield (left half)
  - `fuji44_right` shield (right half)
  - `settings_reset` (for resetting board settings)
- **West manifest**: Uses official ZMK repository (main branch) via `config/west.yml`
- **Shield definition**: Located in `config/boards/shields/fuji44/`

#### ZMK Features Enabled (`config/fuji44.conf`)
- **Bluetooth**:
  - BLE enabled with boosted TX power (+8dBm)
  - Experimental BLE features enabled
- **Power Management**:
  - Idle timeout: 5 minutes (300000ms)
  - Sleep disabled (CONFIG_ZMK_SLEEP=n)
  - Idle sleep timeout: 15 minutes (1800000ms)
- **Battery Monitoring**:
  - Battery reporting enabled (30-second intervals)
  - Split BLE central battery level proxy for monitoring both halves
  - BLE BAS (Battery Service) enabled
- **Combo Support**: Max 3 keys per combo, max 7 combos per key

### Keyboard Layout (Keymap)

The keymap (`config/fuji44.keymap`) implements a custom layout with 5 layers:

#### Layer 0: Default (def)
- Standard typing layer with QWERTY-based layout
- **Home row mods**: Custom hold-tap behaviors for modifiers on home row
  - Left: `hml` behavior (A,S,D,F keys act as modifiers when held)
  - Right: `hmr` behavior (J,K,L,; keys act as modifiers when held)
- **Thumb keys**:
  - Left: Ctrl, Alt, GUI, Layer 1 (Space), Layer 3 (Enter)
  - Right: Layer 2, Alt (with tap-dance for brackets), Brackets

#### Layer 1: Navigation (nav)
- Arrow keys, Page Up/Down, Home/End
- Parentheses, brackets, braces
- Function keys access (via layer combo)

#### Layer 2: Numbers (num)
- Numbers 0-9, symbols (+, -, =, etc.)
- Function keys F1-F12

#### Layer 3: Symbols (sym)
- Special characters: !, @, #, $, %, ^, &, *, (, )

#### Layer 4: System (sys)
- Automatically activated when layers 1+2 are active (conditional layer)
- Bluetooth profile selection (BT_SEL 0-3)
- Bluetooth clear (BT_CLR, BT_CLR_ALL)
- Bootloader and system reset

### Advanced ZMK Behaviors

The configuration uses several advanced ZMK features:

1. **Hold-Tap (Home Row Mods)**:
   - `hml` / `hmr`: Tap-preferred hold-tap with 250ms tapping term
   - Quick tap: 165ms
   - Require prior idle: 130ms
   - Position-aware triggering for better ergonomics

2. **Mod-Morph**:
   - `ss_hs_modmorph`: M key morphs to RIGHT_BRACKET with Shift
   - `bh_fwslsl_lftbrckt`: Forward slash morphs to LEFT_BRACKET with RAlt
   - `bh_bckslsh_rghtbrckt`: Backslash morphs to RIGHT_BRACKET with RAlt
   - `bh_bcksp_del`: Backspace morphs to Delete with RAlt

3. **Tap-Dance**:
   - `kh_hs_tapdance`: Single tap = LEFT_BRACKET, double tap = RIGHT_BRACKET (200ms window)

4. **Combos**:
   - Combo ESC: Keys 0+1 → ESC (50ms timeout)
   - RU/EN switch: Keys 24+25 → LC(LA(SPACE)) for language toggle

5. **Conditional Layers**:
   - System layer (layer 4) automatically activates when both nav (1) and num (2) layers are active

### Project Structure

```
├── AGENTS.md                    # Project documentation
├── build.yaml                   # Build configuration
├── config/
│   ├── fuji44.conf              # ZMK configuration options
│   ├── fuji44.json              # Keymap JSON (for ZMK Studio)
│   ├── fuji44.keymap            # Main keymap definition
│   ├── west.yml                 # West manifest for ZMK
│   └── boards/shields/fuji44/
│       ├── fuji44.dtsi          # Common device tree definitions
│       ├── fuji44.yml           # Shield metadata
│       ├── fuji44_left.conf     # Left half config (empty)
│       ├── fuji44_left.overlay  # Left half device tree overlay
│       ├── fuji44_right.conf    # Right half config (empty)
│       ├── fuji44_right.overlay # Right half device tree overlay
│       ├── Kconfig.defconfig    # Kconfig default configuration
│       └── Kconfig.shield       # Kconfig shield options
└── 3dmodel/
    ├── fuji.scad                # OpenSCAD 3D model source
    └── fuji.stl                 # Exported STL for printing
```

### 3D Model

The project includes a 3D printable case model:
- **Source**: `3dmodel/fuji.scad` (OpenSCAD parametric design)
- **Export**: `3dmodel/fuji.stl` (ready for 3D printing)
- Custom designed for the Fuji44 PCB and components

### Development Notes

- **Website**: https://oumae.ru
- **Shield ID**: fuji44
- **Compatible with**: pro_micro footprint (nice_nano)
- **ZMK Studio**: Supported (feature enabled in shield metadata)
- **Siblings**: The shield defines `fuji44_left` and `fuji44_right` as sibling shields for split operation

