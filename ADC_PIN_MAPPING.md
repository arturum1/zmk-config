# ADC Pin Mapping Reference for TenStar ProMicro NRF52840

## Important: Verify Your Board's Analog Pin Mapping

The analog pin labels (A1, A2, A3) on ProMicro-compatible boards map to specific nRF52840 GPIO pins and SAADC channels.

### Typical Nice!Nano v2 / ProMicro NRF52840 Mapping:
| Arduino Pin | nRF52840 GPIO | SAADC AIN | Zephyr DT Channel |
|-------------|---------------|-----------|-------------------|
| A1 (D19)    | P0.03         | AIN1      | channel@1         |
| A2 (D20)    | P0.04         | AIN2      | channel@2         |
| A3 (D21)    | P0.28         | AIN4      | channel@4         |

### Current Configuration Uses:
The overlay files are configured with:
- `channel@6` using `NRF_SAADC_AIN6` (P0.30)
- `channel@5` using `NRF_SAADC_AIN5` (P0.29)
- `channel@0` using `NRF_SAADC_AIN0` (P0.02)

### Action Required:
**Verify your TenStar board's analog pin mapping** by checking:
1. Board documentation/schematic
2. The actual wiring between your multiplexer outputs and the MCU board

If your board uses standard Nice!Nano v2 mapping (A1→AIN1, A2→AIN2, A3→AIN4), update the overlay files:
- Change `channel@6` to `channel@1` with `NRF_SAADC_AIN1`
- Change `channel@5` to `channel@2` with `NRF_SAADC_AIN2`
- Change `channel@0` to `channel@4` with `NRF_SAADC_AIN4`

### GPIO Select Pin Mapping (D4-D7):
| Arduino Pin | nRF52840 GPIO | Zephyr DT Node |
|-------------|---------------|----------------|
| D4          | P0.22         | `&gpio0 22`    |
| D5          | P0.24         | `&gpio0 24`    |
| D6          | P1.00         | `&gpio1 0`     |
| D7          | P0.11         | `&gpio0 11`    |

These mappings are correctly configured in the overlay files.

## nRF52840 Anomaly 212 Warning
Some nRF52840 chips (build codes CKAA-Dx0 or QIAA-Dx0) have a bug where switching between single-channel and multi-channel ADC mode causes the ADC to stop working. If you experience ADC issues:
1. Check your chip's build code
2. Consider using a single ADC channel with an external ADC (e.g., MCP3204 via SPI) if affected
