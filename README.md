# CC1101 RF Remote Control Template for ESPHome

## Overview

This is a reusable ESPHome template for CC1101 RF transceiver modules. It provides a complete RF remote control system with transmission, reception, and configurable button codes. Perfect for controlling garage doors, gates, or other RF-controlled devices.

## Features

- **Dual RF Buttons**: Two independently configurable RF transmission codes
- **Dry Contact Input**: Physical button input triggers RF transmissions
- **Listen Mode**: Built-in RF receiver to capture codes from existing remotes
- **Manual Control**: Home Assistant buttons for testing and manual operation
- **Frequency Configuration**: Set frequency in Hz (300-928 MHz depending on CC1101 model)
- **Full Pin Configuration**: All pins are configurable via substitutions

## Hardware Requirements

- ESP32 or ESP8266 board
- CC1101 RF transceiver module (433 MHz recommended)
- Proper wiring for SPI communication

## Pin Configuration

The CC1101 requires the following connections:
- **SPI Bus**: CLK, MOSI, MISO pins
- **CS**: Chip Select pin
- **GDO0**: Data pin for transmission (remote_transmitter)
- **GDO2**: Data pin for reception (remote_receiver)

## Usage

### 1. Include in Your ESPHome Configuration

```yaml
packages:
  genfobber: !include
    file: path/to/yambms_genfobber.yaml
    vars:
      # CC1101 Configuration
      cc1101_frequency: "433920000"  # 433.92 MHz in Hz

      # SPI Pin Configuration
      spi_clk_pin: "14"
      spi_mosi_pin: "11"
      spi_miso_pin: "13"

      # CC1101 Pin Configuration
      cc1101_cs_pin: "21"
      cc1101_gdo0_pin: "1"   # GDO0 for TX
      cc1101_gdo2_pin: "42"  # GDO2 for RX

      # Dry Contact Input Pin
      dry_contact_pin: "38"
      dry_contact_inverted: "true"

      # Start/Stop Generator Transmission Codes (captured from your remote)
      start_generator_code: "339, -1161, 1071, -413, ..."
      stop_generator_code: "330, -1143, 1064, -419, ..."
```

### 2. Capturing RF Codes from Your Remote

1. Flash the firmware with the template included
2. In Home Assistant, toggle the "CC1101 Listen Mode" switch ON
3. Press your physical remote button
4. Check the ESPHome logs for the captured raw codes
5. Copy the raw code array and paste into your configuration as `start_generator_code` or `stop_generator_code`
6. Recompile and flash

Example log output:
```
[remote.raw:028]: Received Raw: 339, -1161, 1071, -413, 1062, -423, ...
```

### 3. Testing Your Configuration

Once configured:
- Press the physical dry contact button to trigger transmission
- Or use the Home Assistant buttons: "Transmit Button 1" and "Transmit Button 2"
- Monitor the logs to verify transmission is occurring

## Configuration Variables

All variables with their default values:

| Variable | Default | Description |
|----------|---------|-------------|
| `cc1101_frequency` | `"433920000"` | Frequency in Hz (433.92 MHz) |
| `spi_clk_pin` | `"14"` | SPI Clock pin |
| `spi_mosi_pin` | `"11"` | SPI MOSI pin |
| `spi_miso_pin` | `"13"` | SPI MISO pin |
| `cc1101_cs_pin` | `"21"` | CC1101 Chip Select |
| `cc1101_gdo0_pin` | `"1"` | GDO0 for TX |
| `cc1101_gdo2_pin` | `"42"` | GDO2 for RX |
| `dry_contact_pin` | `"38"` | Dry contact input |
| `dry_contact_inverted` | `"true"` | Invert dry contact logic |
| `start_generator_code` | *(example)* | RF code to start generator |
| `stop_generator_code` | *(example)* | RF code to stop generator |
| `tx_repeat_times` | `"3"` | Times to repeat transmission |
| `tx_repeat_wait` | `"15ms"` | Wait between repeats |

## Entities Created

- **Switch**: "CC1101 Listen Mode" - Toggle RF receiver
- **Binary Sensor**: "Dry Contact" - Status of physical button
- **Button**: "Start Generator" - Manual trigger to start
- **Button**: "Stop Generator" - Manual trigger to stop

## Common Frequencies

- **433.92 MHz**: `"433920000"` (most common ISM band)
- **315 MHz**: `"315000000"` (common in North America)
- **868 MHz**: `"868000000"` (common in Europe)
- **915 MHz**: `"915000000"` (common in North America)

## Troubleshooting

### "Pin X is used in multiple places"
- GDO0 pin is intentionally shared between cc1101 and remote_transmitter
- Ensure `allow_other_uses: true` is set on both components (already configured in template)

### No transmission occurring
- Check pin connections
- Verify frequency matches your device (433.92 MHz most common)
- Ensure antenna is connected to CC1101
- Check logs for "Starting transmission" messages

### Can't capture codes
- Enable "Listen Mode" switch
- Ensure GDO2 pin is properly connected
- Check that frequency matches your remote
- Remote must be close to CC1101 antenna

## Hardware Compatibility

Tested with:
- ESP32-S3 (LilyGo T-Connect)
- CC1101 433MHz modules
- Common garage door/gate RF remotes

## License

This template is provided as-is for use in ESPHome projects.
