# MOCO-Display

ESP32-S3 IDF-based firmware for the MOCO jib crane main control panel display.

## Overview

MOCO-Display provides the LVGL-based touchscreen UI for the motion control system, allowing operators to:
- Configure camera motion parameters (pan, tilt, swing, lift, focus)
- Set timelapse interval and shooting duration
- Adjust motor speed and acceleration
- Monitor system status and alerts

## Hardware

- **MCU**: ESP32-S3 with 8MB PSRAM
- **Board**: [Waveshare ESP32-S3-Touch-AMOLED-2.41](https://a.co/d/02X1OVXf) — 2.41" AMOLED (600×450), 16MB Flash, 8MB PSRAM
- **Interface**: I2C for touch, SPI for display

## Build & Flash

### Prerequisites
- ESP-IDF v5.0+ installed and configured
- ESP32-S3 development board connected via USB

### Build
```bash
idf.py build
```

### Flash
```bash
idf.py flash -p /dev/cu.usbmodem1301
```

### Monitor Serial Output
```bash
idf.py monitor -p /dev/cu.usbmodem1301
```

## Configuration

Key display settings located in `lv_conf.h`:
- Color depth and rendering options
- Font selections (Montserrat sizes 40, 120, 150)
- Performance tuning for LVGL

Hardware-specific settings in `WaveshareBoardConfig.h`:
- GPIO pin assignments
- SPI/I2C bus configuration
- LCD controller parameters

## Project Structure

```
ESP32-S3/
├── main/                      # IDF component entry point
│   ├── main.c                # Settings UI and controller loops
│   ├── CMakeLists.txt
│   └── lv_conf.h             # LVGL config
├── DisplayApp.cpp/.h         # Display rendering layer
├── DisplayBackend.cpp/.h     # Hardware abstraction
├── esp_lcd_sh8601.c/.h       # LCD driver (SH8601 controller)
├── lv_conf.h                 # LVGL configuration
├── partitions.csv            # Flash partitioning
└── CMakeLists.txt            # Build config
```

## Development

The firmware provides a settings menu accessible via PS2 controller input (received via UART from the Mega master):
- **Directional buttons**: Navigate settings
- **O/X buttons**: Confirm/Cancel dialogs
- **L1/R1 buttons**: Jump between settings sections

Serial protocol communicates settings changes back to the Mega master for motor control.

## License

MIT License - See LICENSE file

