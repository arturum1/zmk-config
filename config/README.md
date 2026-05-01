# ZMK Configuration for 44-Key Split Hall Effect Keyboard

This repository contains the ZMK firmware configuration for a custom split keyboard using Hall Effect sensors and Nice!Nano v2 controllers.

## Hardware Configuration

- **Controller**: Nice!Nano v2 (nRF52840)
- **Matrix**: 44 sensors per half, multiplexed via CD74HC4067 (3 mux per side)
- **Multiplexer Pins**:
  - S0-S3: D4, D5, D6, D7 (P0.22, P0.24, P1.00, P0.11)
- **ADC Channels**:
  - Mux 0 → A3 (AIN4 / Channel 4)
  - Mux 1 → A2 (AIN2 / Channel 2)
  - Mux 2 → A1 (AIN1 / Channel 1)
- **RGB Underglow**: MOSI on D2 (P0.06), SCK on D3 (P0.17)

## Project Structure

- `config/my_keyboard.keymap`: Unified keymap for both halves.
- `config/my_keyboard.conf`: Global configuration (Split, HE, RGB).
- `config/boards/shields/`: Shield definitions and hardware overlays.
  - `my_keyboard.dtsi`: Shared hardware definitions and matrix transform.
  - `my_keyboard_left.overlay`: Left half hardware config.
  - `my_keyboard_right.overlay`: Right half hardware config (with row offset).

## Key Features

- **Adjustable Actuation**: Each key uses the `&he_standard` behavior, allowing you to set press/release thresholds (default: 50%/45%).
- **Split Communication**: Central (Right) and Peripheral (Left) communicate wirelessly via BLE.
- **Ratiometric ADC**: Measurement is referenced to VDD to ensure stability across battery voltage levels.

## Flashing Instructions

1. Build the firmware using GitHub Actions.
2. Download the artifacts:
   - `my_keyboard_left-nice_nano_v2-zmk.uf2`
   - `my_keyboard_right-nice_nano_v2-zmk.uf2`
3. Connect the **Right half** (Central) and flash its UF2 file.
4. Connect the **Left half** (Peripheral) and flash its UF2 file.
5. They will automatically pair via Bluetooth.

## Calibration

Use the ZMK serial console to perform runtime calibration of your Hall Effect sensors. This will save the optimal `v-min` and `v-max` values to the chip's NVS memory.
