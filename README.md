# ZMK Custom Hall Effect Keyboard (nRF52840)

This repository contains the ZMK firmware configuration for a custom-built 48-key Hall Effect (analog) keyboard. The project uses an **nRF52840 (Tenstar Pro Micro)** MCU, three **16-channel analog multiplexers (CD74HC4067)**, and **SS49E Hall Effect sensors**.

## 🛠 Hardware Architecture

* **MCU:** Tenstar Pro Micro (nRF52840) - compatible with `nice_nano_v2`.
* **Scanning:** 3x 16-channel Multiplexers.
    * **Select Pins (S0-S3):** Shared across all muxes to Pro Micro pins 4, 5, 6, 7.
    * **Analog Outputs:** Connected to ADC channels AIN6, AIN5, and AIN0.
* **Sensors:** SS49E Hall Effect sensors (Ratiometric).
* **RGB:** WS2812B LED strip connected to Pin 3.

## 📂 Key Project Files

If you want to modify your keyboard in the future, these are the files you need to know about:

### 1. `boards/shields/my_keyboard/my_keyboard.overlay`
**Purpose:** Defines the hardware "map."
- **Edit this if:** You change which physical pins are used on the nRF52840, change the number of multiplexers, or change the number of LEDs in your strip.
- **Key Sections:** `kscan0` (Multiplexer pins), `adc` (ADC reference and gain settings), and `led_strip`.

### 2. `boards/shields/my_keyboard/my_keyboard.keymap`
**Purpose:** Defines the logic of your keys.
- **Edit this if:** You want to change what a key does (e.g., change 'A' to 'B'), add new layers, or adjust **Actuation Behaviors** (how deep you press before a key registers).
- **Format:** Uses `&kp` for standard keys and custom behaviors for analog features like Rapid Trigger.

### 3. `config/my_keyboard.conf`
**Purpose:** Global firmware settings.
- **Edit this if:** You want to enable/disable features like Bluetooth Sleep, RGB Underglow, or the Hall Effect module itself.
- **Key Flags:** `CONFIG_ZMK_HALL_EFFECT`, `CONFIG_ZMK_RGB_UNDERGLOW`.

### 4. `build.yaml`
**Purpose:** Tells GitHub Actions what to compile.
- **Edit this if:** You rename your shield or want to build for a different board.

## ⚡ Calibration & Ratiometric Sensing

This project uses **Ratiometric ADC Sensing**. The ADC reference is set to `VDD/4` and Gain is set to `1/4`. This ensures that sensor readings remain consistent even if the battery voltage fluctuates.

### How to Calibrate:
1. Connect the keyboard via USB.
2. Open a serial terminal (e.g., `screen /dev/ttyACM0 115200`).
3. Use the Hall Effect module commands to record `v-min` (rest) and `v-max` (fully depressed) for your specific sensors.
4. These values are stored in the nRF52840's Non-Volatile Storage (NVS) and persist across reboots.

## 🚀 Building & Flashing

This repo is set up for **GitHub Actions**:
1. Push your changes to the `main` branch.
2. Download the `.uf2` file from the **Actions** tab on GitHub.
3. Double-tap Reset on your keyboard to enter bootloader mode.
4. Drag and drop the `.uf2` file into the USB drive.

---
*Note: This project utilizes the [zmk-feature-hall-effect](https://github.com/cr3eperall/zmk-feature-hall-effect) community module.*
