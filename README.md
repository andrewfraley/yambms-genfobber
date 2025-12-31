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

Include this module in your YamBMS configuration as a remote package:

```yaml
packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
```

## Configuration

### Pin Override Example

```yaml
# Override default pins before including the package
globals:
  - id: sck_pin
    type: int
    initial_value: '18'
  - id: miso_pin
    type: int
    initial_value: '19'
  - id: mosi_pin
    type: int
    initial_value: '23'
  - id: csn_pin
    type: int
    initial_value: '5'
  - id: gdo0_pin
    type: int
    initial_value: '2'
  - id: dry_contact_pin
    type: int
    initial_value: '15'

packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main
```

### RF Code and Frequency Override Example

To use your own RF codes and adjust the TX frequency:

```yaml
globals:
  - id: generator_start_code
    type: std::vector<int>
    initial_value: '[your, captured, raw, codes, here]'
  - id: generator_stop_code
    type: std::vector<int>
    initial_value: '[your, captured, raw, codes, here]'

packages:
  generator_control:
    url: https://github.com/yourusername/yambms-genfobber
    file: yambms_genfobber.yaml
    ref: main

# Override TX frequency after including the package
number:
  - id: tx_frequency
    initial_value: 433.92  # Adjust to your frequency
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
5. Update the `generator_start_code` global in your YAML with the captured codes
6. Repeat for the stop button
7. Disable listen mode when done

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
