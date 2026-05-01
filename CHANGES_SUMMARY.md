# ZMK Configuration Changes Summary

## Issues Fixed

### 1. Added Hall Effect Module (Critical)
**File**: `config/west.yml`
- Added `zmk-feature-hall-effect` module from `https://github.com/cr3eperall`
- This module provides the `he,kscan-multiplexer` driver required for CD74HC4067 multiplexer support
- Also provides `he,behavior-adjustable-actuation` for configurable actuation points

### 2. Split Keyboard Structure (Critical)
**Created**:
- `boards/shields/my_keyboard_left/` - Left half shield
- `boards/shields/my_keyboard_right/` - Right half shield

**Files per shield**:
- `Kconfig.shield` - Shield identification
- `Kconfig.defconfig` - Split role configuration (left=peripheral, right=central)
- `my_keyboard_left.overlay` / `my_keyboard_right.overlay` - Hardware configuration
- `my_keyboard_left.keymap` / `my_keyboard_right.keymap` - Key mappings

**File**: `config/my_keyboard.conf`
- Added split keyboard configs:
  - `CONFIG_ZMK_SPLIT=y`
  - `CONFIG_ZMK_SPLIT_BLE=y` (wireless split communication)
  - `CONFIG_ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS=1`
  - `CONFIG_ZMK_SPLIT_ROLE_CENTRAL=y`

**File**: `build.yaml`
- Updated to build both left and right shields separately
- Each half gets its own artifact name

### 3. Fixed GPIO References (Critical)
**Files**: `my_keyboard_left.overlay`, `my_keyboard_right.overlay`
- Changed invalid `&pro_micro` references to correct Nice!Nano v2 GPIO controllers:
  - D4 (S0) → `&gpio0 22` (P0.22)
  - D5 (S1) → `&gpio0 24` (P0.24)
  - D6 (S2) → `&gpio1 0` (P1.00)
  - D7 (S3) → `&gpio0 11` (P0.11)

### 4. Adjusted for 44 Sensors (Major)
**Files**: Both overlay and keymap files
- Matrix map updated from 48 to 44 entries
- Mux 0: 16 channels (all used)
- Mux 1: 16 channels (all used)
- Mux 2: 12 channels (channels 12-15 unused)
- Keymap bindings adjusted to exactly 44 entries per half

### 5. Fixed ADC v-min/v-max Values (Major)
**Files**: Both overlay files
- Changed from millivolt values (1730, 2400) to raw 12-bit ADC values:
  - `v-min = <2146>` (was 1730mV, now raw value for ratiometric ADC)
  - `v-max = <2978>` (was 2400mV, now raw value for ratiometric ADC)
- Formula: `(V_mV / 3300) * 4095 = raw_12bit`

### 6. Applied HE Behavior to Keys (Major)
**Files**: Both keymap files
- Changed from plain `&kp KEY` to `&he_standard &kp KEY`
- This enables Hall Effect adjustable actuation (50% press, 45% release thresholds)

### 7. Preserved/Enhanced Comments
All configuration files now include detailed comments explaining:
- GPIO pin mappings with Arduino → nRF52840 → Zephyr translations
- ADC channel configurations and ratiometric measurement explanation
- Split keyboard role assignments
- Unused channel handling
- nRF52840 Anomaly 212 warning (ADC bug in some chips)
- RGB underglow power considerations
- WS2812 level shifter requirements

## New Documentation Files

### `ADC_PIN_MAPPING.md`
- Reference table for analog pin mappings
- Verification instructions for your TenStar board
- Warning about nRF52840 Anomaly 212

## Build Configuration

The configuration now builds two separate firmware files:
1. `my_keyboard_left-nice_nano_v2-zmk.uf2` - Left half (peripheral)
2. `my_keyboard_right-nice_nano_v2-zmk.uf2` - Right half (central)

## Next Steps

1. **Verify ADC Pin Mapping**: Check your TenStar board's analog pin → AIN channel mapping
   - Current config assumes: A1→AIN0, A2→AIN5, A3→AIN6
   - Adjust `channel@` numbers in overlay if different

2. **Flash Both Halves**:
   - Flash right half first (central)
   - Then flash left half (peripheral)
   - They should auto-pair via BLE

3. **Calibrate Sensors**: Use the runtime calibration tool to save optimal v-min/v-max values to NVS

4. **Test Split Communication**: Verify both halves communicate wirelessly

## Wired Split Option

To use wired UART instead of BLE for split communication:
1. Disable: `CONFIG_ZMK_SPLIT_BLE=y`
2. Enable: `CONFIG_ZMK_WIRED_SPLIT=y`
3. Add `zmk,wired-split` node to overlay
4. Connect UART wires between halves
