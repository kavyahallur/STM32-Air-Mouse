
# Air Mouse using STM32F401RE and MPU-6050

A hand-held USB HID motion controller that moves the PC cursor by detecting rotation with no flat surface and no drivers needed. 

## Overview

The MPU-6050 gyroscope communicates with the STM32F401RE Nucleo-64 over I2C. The board appears to the PC as a standard USB HID mouse, sending a 4-byte report at 100 Hz. Four buttons provide left click, right click, scroll up and scroll down.

## System Block Diagram

<img width="1732" height="882" alt="image" src="https://github.com/user-attachments/assets/f8d59960-11b5-418b-9e98-8f1955f69934" />


MPU-6050 -> I2C (PB8/PB9) -> STM32F401RE -> USB HID Full-Speed -> PC cursor

## Hardware

 Component Role

 *STM32F401RE Nucleo-64 - Main controller (Cortex-M4 @ 84 MHz)
 
 *MPU-6050 module - 6-axis IMU, I2C address 0x68 
 *Tactile buttons (x4) - Click and scroll inputs 
 *Micro-USB US- B HID connection to PC 

### Wiring

| MPU-6050 | Nucleo pin | Purpose |
| VCC | 3.3V | Power |
| GND | GND | Ground |
| SDA | PB9 | I2C data |
| SCL | PB8 | I2C clock |
| AD0 | GND | Sets address 0x68 |

| Button | Pin |
| Left click | PA0 |
| Right click | PA1 |
| Scroll up | PB1 |
| Scroll down | PC13 |

Buttons are active-low with internal pull-ups. USB uses PA11/PA12.

## How It Works

1. Wake the MPU-6050 by writing 0x00 to register 0x6B.
2. Burst-read 6 bytes of gyro data from register 0x43.
3. Subtract the measured resting bias.
4. Apply a deadzone filter to remove micro-vibration drift.
5. Scale and clamp to int8, then send the HID report.

## Verification

Tested in six stages: LED blink, UART debug, I2C WHO_AM_I (0x68), live gyro data, USB HID cursor, buttons.

## Challenges and Fixes

- **Sensor drift:** fixed with a 50-sample bias measurement and a deadzone.
- **Axis mapping:** identified through UART diagnostics.
- **I2C HAL_ERROR:** caused by a pin mismatch (PB6/7 vs PB8/9), fixed by reassigning pins in CubeMX.

## Future Work

Accelerometer sensor fusion, Bluetooth LE, gesture recognition and a custom PCB.

