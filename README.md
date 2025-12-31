# CC1101 Generator Control Module for YamBMS

## Overview

This module provides RF-based generator control via a CC1101 transceiver for YamBMS systems. It can be used as a remote package to send start/stop commands to a generator controller via learned RF codes.

## Features

- **Automatic Control**: Dry contact input triggers generator start (contact closes) and stop (contact opens)
- **Manual Control**: Home Assistant buttons to manually send start/stop commands
- **Listen Mode**: Toggle RF receive mode to capture codes from your existing remote
- **Configurable RF Codes**: Override the default RF codes with your own learned codes
- **Frequency Adjustment**: Set TX frequency via YAML (430-440 MHz, default 433.89 MHz)
- **Pin Configuration**: Fully configurable pin assignments

## Usage as YamBMS Remote Package

Include this module in your YamBMS configuration as a remote package. All settings can be customized using the `vars:` parameter:

```yaml
packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
    # All settings are optional - defaults shown below
    vars:
      # CC1101 Pin Configuration
      sck_pin: "18"           # SPI Clock
      miso_pin: "19"          # SPI MISO
      mosi_pin: "23"          # SPI MOSI
      csn_pin: "5"            # Chip Select
      gdo0_pin: "2"           # CC1101 GDO0 Data I/O
      dry_contact_pin: "15"   # Dry contact input
      # RF Transmission Settings
      tx_frequency: "433.89"  # TX frequency in MHz
      # Generator RF Codes (replace with your captured codes)
      generator_start_code: '[your, start, codes, here]'
      generator_stop_code: '[your, stop, codes, here]'
```

## Configuration Examples

### Minimal Configuration (using all defaults)

```yaml
packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
```

### Custom Pin Configuration

```yaml
packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
    vars:
      sck_pin: "14"
      miso_pin: "12"
      mosi_pin: "13"
      csn_pin: "15"
      gdo0_pin: "4"
      dry_contact_pin: "16"
```

### Custom RF Codes and Frequency

```yaml
packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
    vars:
      tx_frequency: "433.92"
      generator_start_code: '[339, -1161, 1071, -413, 1062, -423, 321, ...]'
      generator_stop_code: '[330, -1143, 1064, -419, 1075, -413, 337, ...]'
```

## Home Assistant Controls

Once added to your YamBMS system, you'll have access to:

- **Generator RF Listen Mode** (switch): Enable to capture RF codes from your existing remote
- **Generator Start** (button): Manually send the start command
- **Generator Stop** (button): Manually send the stop command

Both manual buttons automatically disable listen mode before transmitting.

## Learning RF Codes

To capture your generator remote's RF codes:

1. Enable the **"Generator RF Listen Mode"** switch in Home Assistant
2. Monitor the ESPHome logs
3. Press your generator remote's start button
4. Copy the raw codes from the logs (look for `remote_receiver` output)
5. Update the `generator_start_code` in the `vars:` section of your YAML with the captured codes
6. Repeat for the stop button
7. Disable listen mode when done
8. Recompile and upload your firmware

Example of captured codes in logs:
```
[remote.raw:059]: Received Raw: [339, -1161, 1071, -413, 1062, ...]
```

Copy the entire array including brackets and add to your vars:
```yaml
vars:
  generator_start_code: '[339, -1161, 1071, -413, 1062, ...]'
```

## Hardware Connections

- **SCK**: SPI Clock (default: GPIO 18)
- **MISO**: SPI Master In Slave Out (default: GPIO 19)
- **MOSI**: SPI Master Out Slave In (default: GPIO 23)
- **CSN**: Chip Select (default: GPIO 5)
- **GDO0**: Data I/O (default: GPIO 2)
- **Dry Contact**: Generator control input (default: GPIO 15)

## Dry Contact Behavior

- **Contact Closes**: Sends generator start command
- **Contact Opens**: Sends generator stop command
