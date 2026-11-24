# Adaptive Street Light Control System

## Project Overview

The Adaptive Street Light Control System is an ARM7-based embedded systems mini project developed using Embedded C and the LPC2148 microcontroller.

The system automatically controls street lights based on the Real-Time Clock (RTC) schedule and surrounding light intensity detected through an LDR sensor.

The street lights turn ON when the configured time schedule is active and the surrounding environment is dark.

## Hardware Components

- LPC2148 ARM7 Microcontroller
- LDR Sensor
- RTC (Real-Time Clock)
- 16x2 LCD Display
- 4x4 Matrix Keypad
- External Interrupt Push Button
- Street Light LEDs
- ADC Interface
- EPROM / memory component (if integrated in the final hardware)

## Software Requirements

- Embedded C
- Keil µVision IDE
- LPC2148 Device Support
- Flash Magic (for programming, if used)

## Project Features

1. Automatic street light control using RTC and LDR.
2. Real-time time and date display on LCD.
3. Keypad-based time and date configuration.
4. User-configurable street light ON and OFF schedule.
5. External interrupt-based menu access.
6. ADC-based LDR intensity measurement.
7. Hysteresis-based light detection to reduce unnecessary switching.
8. Modular programming using separate C source and header files.

## Working Principle

1. The LPC2148 microcontroller initializes the LCD, keypad, RTC, ADC, LDR and street light modules.
2. RTC maintains the current time and date.
3. The user can configure RTC values and street light schedule using the keypad.
4. The LDR sensor detects surrounding light intensity through the ADC.
5. The microcontroller checks whether the current time falls within the configured schedule.
6. It also checks whether the environment is dark.
7. When both conditions are satisfied, street lights are switched ON.
8. Otherwise, street lights remain OFF.

## Software Modules

| Module | Description |
|---|---|
| main.c | Main application |
| adc.c / adc.h | ADC configuration and conversion |
| ldr.c / ldr.h | LDR sensor processing |
| rtc.c / rtc.H | RTC configuration and time management |
| lcd.c / lcd.h | LCD display operations |
| kpm.c / kpm.h | Matrix keypad operations |
| menu.c / menu.h | User menu and configuration |
| eint.c / eint.h | External interrupt configuration |
| streetlight.c / streetlight.h | Street light control |
| delay.c / delay.h | Delay functions |
| project_config.h | Project configuration |
| types.h | User-defined data types |

## Default Configuration

- Microcontroller: LPC2148
- LDR ADC Channel: AD0.1
- LDR Dark Threshold: 650
- LDR Bright Threshold: 550
- Street Light Start Time: 18:30
- Street Light End Time: 06:00
- Street Light Output Pins: P1.24 to P1.27

These values are based on the supplied source code and may be adjusted according to hardware calibration.

## Future Enhancements

- Energy consumption monitoring.
- Wireless monitoring and control.
- Fault detection in street light systems.
- Memory-based storage of user-configured settings.

## Author

**Project:** Adaptive Street Light Control System

**Microcontroller:** LPC2148 ARM7

**Programming Language:** Embedded C

**Development Environment:** Keil µVision

**Project Type:** ARM Embedded Systems Mini Project

## Repository Contents

This repository contains the Embedded C source files, header files and project documentation required for the Adaptive Street Light Control System.
