# MOCO-display

ESP32-S3 IDF-based firmware for the MOCO jib crane main control panel display.

## Overview

MOCO-display provides the LVGL-based touchscreen UI for the motion control system, allowing operators to:
- Configure camera motion parameters (pan, tilt, swing, lift, focus)
- Set timelapse interval and shooting duration
- Adjust motor speed and acceleration
- Monitor system status and alerts

## Hardware

- **Board**: [Waveshare ESP32-S3-Touch-AMOLED-2.41](https://a.co/d/02X1OVXf) — 2.41" AMOLED (600×450), 16MB Flash, 8MB PSRAM
- **Interface**: I2C for touch, QSPI for display

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
MOCO-display/
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

## Display Screens & Messages

### Main Screens
| Screen | Description |
|--------|-------------|
| **Logo** | Default idle screen — shows MOCO-jib logo |
| **Settings menu** | Full-screen settings panel (groups: Brightness, System, Timelapse, Zeroing) |
| **Settings editor** | In-place value editor with +/− controls and DONE button |

### Mode Messages (brief overlay, then returns to logo)
| Message | Trigger |
|---------|---------|
| `MODE\nMANUAL` | Switched to manual control |
| `MODE\nDRONE` | Drone mode activated |
| `MODE\nTIMELAPSE\n<variant>` | Timelapse mode — variant describes movement pattern |
| `MODE\nBOUNCE\n<variant>` | Bounce mode — variant describes movement pattern |

### Timelapse Variants
`Standard`, `Pan Left`, `Pan Right`, `Swing Left`, `Swing Right`, `Boom Up`, `Boom Down`, `Pan Left + Boom Up`, `Pan Left + Boom Down`, `Pan Right + Boom Up`, `Pan Right + Boom Down`

### Bounce Variants
`Swing Left + Boom Up`, `Swing Left + Boom Down`, `Swing Right + Boom Down`, `Swing Right + Boom Up`, `Swing Left + Boom Down`, `Swing Left`, `Boom Up`, `Swing Right`, `Boom Down`

### Drone Mode Screen
Live joystick position indicators for Swing/Lift (left stick) and Pan/Tilt (right stick). Includes a Flowlapse panel showing waypoint count, playback progress `%`, and ETA.

### Status / Notification Messages (temporary overlay)
| Message | Trigger |
|---------|---------|
| `EMERGENCY\nSTOP` | Emergency stop activated |
| `EMERGENCY STOP\nRELEASED` | Emergency stop released |
| `NO CONTROLLER\nFOUND` | Controller connection lost |
| `TIMELAPSE\nINTERVAL\n<n>s` | Timelapse interval changed |
| `TIMELAPSE\nSTEP DIST\n<n>ms` | Timelapse step distance changed |
| `RUMBLE\nMUTE ON` / `RUMBLE\nMUTE OFF` | Rumble feedback toggled |
| `HOME\nSET` | Home position saved |
| `RETURNING\nHOME` | Moving to home position |
| `HOME\nREACHED` | Arrived at home position |
| `HOME\nCLEARED` | Home position cleared |
| `HOME\nNOT SET` | Home action attempted with no home saved |
| `HOME ACTION\nBLOCKED` | Home action blocked (e.g. during motion) |

### Control Confirmation Messages (brief overlay)
Displayed when axis speed or direction is changed via the controller:

`FOCUS LEFT/RIGHT`, `FOCUS SPEED UP/DOWN`, `PAN+SWING SPEED UP/DOWN`, `LIFT+TILT SPEED UP/DOWN`, `SWING SOLO LEFT/RIGHT`, `PAN SOLO LEFT/RIGHT`, `SWING+PAN LEFT/RIGHT`, `LIFT SOLO UP/DOWN`, `LIFT+TILT UP/DOWN`, `TILT SOLO UP/DOWN`, `L3 BOUNCE ENDPOINT SET`

### Settings Confirm Dialogs
- **Save changes?** — SAVE / CANCEL
- **Reset all settings to defaults?** — YES / NO (defaults to NO)

## Development

The firmware receives UART commands from the Mega master and renders status on the display. PS2 controller input is relayed via the Mega:
- **Directional buttons**: Navigate settings
- **O/X buttons**: Confirm/Cancel dialogs
- **L1/R1 buttons**: Jump between settings groups

Settings changes are persisted to NVS flash and sent back to the Mega for motor control.

## License

MIT License - See LICENSE file

