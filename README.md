# Adaptive Street Light Control Using Time and Environmental Illumination

## 1. Project Overview

The **Adaptive Street Light Control System** is an intelligent embedded system designed to automate street lighting based on real-time scheduling and environmental illumination.

The system uses an LPC2148 ARM7 microcontroller to integrate a Real-Time Clock (RTC), Light Dependent Resistor (LDR), Analog-to-Digital Converter (ADC), LCD display, matrix keypad, external interrupt, and LED-based street light outputs.

Unlike conventional street lighting systems that operate using fixed timings or manual switching, this project combines time-based scheduling with ambient light detection to improve energy utilization.

The street lights are activated only when the configured RTC schedule is active and the surrounding environment is dark.

The user can configure RTC parameters and street light operating timings through an interrupt-driven menu interface using a matrix keypad.

---

## 2. Project Objectives

- Develop an intelligent automatic street lighting system using an LPC2148 microcontroller.
- Implement RTC-based street light scheduling.
- Monitor surrounding illumination using an LDR sensor.
- Acquire analog sensor measurements using the internal ADC peripheral.
- Automatically control street lights according to time and environmental conditions.
- Provide a menu-driven configuration interface using a keypad.
- Implement external interrupt-based menu entry.
- Display RTC information and system status through an LCD.
- Reduce unnecessary power consumption by avoiding street light operation when sufficient ambient illumination is available.

---

## 3. Hardware Requirements

| Component | Purpose |
|---|---|
| LPC2148 ARM7 Microcontroller | Main processing and control unit |
| LDR Sensor | Detects surrounding illumination |
| ADC (AD0.1) | Converts LDR analog voltage into digital data |
| RTC Peripheral | Maintains date and time |
| 16×2 LCD | Displays time, date, settings and system information |
| 4×4 Matrix Keypad | User input and configuration |
| Push Button | Generates EINT0 interrupt request |
| LEDs | Represent street light outputs |
| External Driver Circuit | Interfaces GPIO outputs with the actual lighting load |

## 4. Software Requirements

| Software / Tool | Purpose |
|---|---|
| Embedded C | Application development |
| Keil µVision | Embedded C development and compilation |
| ARM7 Compiler Toolchain | Generates target executable |
| Flash Magic | Programs the generated HEX file into LPC2148 |
| Git and GitHub | Source code management and project documentation |

---

## 5. System Architecture

The project is divided into independent software modules. Each module handles a specific peripheral or application responsibility.

### Main Functional Modules

**1. Main Application Module (`main.c`)**

- Acts as the entry point of the application.
- Initializes the required peripherals and drivers.
- Coordinates the RTC, LDR, keypad, LCD, menu and street light modules.
- Executes the background application tasks.

**2. RTC Module (`rtc.c`, `rtc.h`)**

- Initializes the Real-Time Clock peripheral.
- Configures the RTC clock and prescaler.
- Sets and retrieves time information.
- Sets and retrieves date information.
- Maintains the day-of-week information.
- Provides leap-year and days-in-month calculations.
- Displays RTC information on the LCD.

**3. LDR / ADC Module (`ldr.c`, `ldr.h`)**

- Initializes the ADC peripheral.
- Reads analog voltage from the LDR sensor.
- Converts ADC measurements into usable digital values.
- Determines the surrounding illumination condition.
- Uses configured darkness and brightness thresholds.
- Maintains sensor initialization and ADC validity status.

**4. LCD Module (`lcd.c`, `lcd.h`)**

- Initializes the LCD.
- Sends commands and data to the LCD.
- Displays strings and numerical values.
- Displays RTC information and menu options.
- Provides the user interface for system monitoring.

**5. Keypad Module (`kpm.c`, `kpm.h`)**

- Initializes the matrix keypad GPIO pins.
- Scans keypad rows and columns.
- Detects key presses.
- Provides numeric input for RTC and schedule configuration.
- Supports the menu's input and editing operations.

**6. External Interrupt Module (`eint.c`, `eint.h`)**

- Configures the external interrupt input.
- Configures the VIC interrupt channel.
- Detects the dedicated menu-entry push button.
- Requests entry into the configuration menu.

**7. Menu Module (`menu.c`, `menu.h`)**

- Implements the user configuration interface.
- Allows editing of RTC time and date.
- Allows configuration of street light ON and OFF timings.
- Reads numeric values from the keypad.
- Validates entered values.
- Supports clear, backspace and accept operations.
- Maintains background street light operation during menu interaction.

**8. Street Light Control Module (`streetlight.c`, `streetlight.h`)**

- Initializes the street light GPIO pins.
- Maintains configurable ON and OFF schedule boundaries.
- Determines whether the current RTC time is within the configured schedule.
- Supports normal and overnight scheduling.
- Combines RTC and LDR conditions.
- Controls the street light output pins.

**9. Delay Module (`delay.c`, `delay.h`)**

- Provides software delay functions.
- Supports timing requirements for LCD, keypad and other peripheral operations.

---

## 6. Hardware Pin Configuration

The following pin assignments represent the project configuration discussed during development. Verify them against the final hardware connections and LPC2148 datasheet before deployment.

| Peripheral | LPC2148 Pin | Function |
|---|---|---|
| LDR / ADC | P0.28 | AD0.1 |
| LCD Data | P0.8 – P0.15 | 8-bit LCD data bus |
| LCD RS | P0.4 | Register Select |
| LCD RW | P0.17 | Read / Write |
| LCD EN | P0.18 | Enable |
| Keypad Rows | P1.16 – P1.19 | Row scanning |
| Keypad Columns | P1.20 – P1.23 | Column detection |
| EINT0 | P0.16 | External interrupt input |
| Street Light Outputs | P1.24 – P1.27 | LED control |

**Hardware interface note:** The GPIO output levels must be compatible with the external LED or lamp driver circuit. Verify the active-HIGH or active-LOW configuration of the connected hardware.

---

## 7. Working Principle

The system operates using two major decision-making conditions:

1. RTC-based scheduling.
2. Environmental illumination detection.

Both conditions must be satisfied before activating the street lights.

### Step 1: System Initialization

When the microcontroller is powered ON:

- RTC peripheral is initialized.
- LCD is initialized.
- Keypad is initialized.
- ADC peripheral is initialized.
- LDR driver is initialized.
- External interrupt is configured.
- Street light GPIO pins are initialized.
- Street light outputs are initially switched OFF.

### Step 2: RTC Monitoring

The RTC continuously maintains the current time and date.

The application retrieves the current hour, minute and second values.

The current RTC time is used to determine whether the configured street light operating schedule is active.

### Step 3: Schedule Evaluation

The system compares the current time with the user-configured ON and OFF timings.

For example:

- ON Time: 18:30
- OFF Time: 06:00

The system supports two types of schedules.

**Normal Schedule:**

Example: 08:00 to 18:00.

The schedule is active when:

`current_time >= start_time && current_time < end_time`

**Overnight Schedule:**

Example: 18:30 to 06:00.

The schedule is active when:

`current_time >= start_time || current_time < end_time`

This logic allows the system to operate correctly across midnight.

**Equal boundaries:**

When the start time and end time are equal, the current implementation treats the schedule as disabled.

### Step 4: Environmental Illumination Detection

The LDR continuously senses the surrounding illumination.

Its analog output is connected to the LPC2148 ADC input.

The ADC converts the analog voltage into a digital value.

The LDR driver processes the ADC measurement to determine whether the environment is dark or bright.

The configured threshold and hysteresis mechanism help prevent unnecessary switching when illumination fluctuates around the decision boundary.

### Step 5: Street Light Decision

The street light control module evaluates the following conditions:

1. ADC measurement must be valid.
2. LDR driver must be initialized.
3. RTC schedule must be active.
4. LDR must indicate darkness.

The logical decision is:

`LIGHTS_ON = ADC_VALID && LDR_INITIALIZED && RTC_SCHEDULE_ACTIVE && LDR_DARK`

If all conditions are TRUE, the street light outputs are activated.

If any condition is FALSE, the street light outputs are deactivated.

If ADC data is invalid or the LDR driver is not initialized, the current implementation defaults to lights OFF.

### Step 6: LCD Monitoring

The LCD provides a user interface for monitoring the system.

It can display:

- Current time.
- Current date.
- Day of the week.
- Configuration menu options.
- Entered RTC values.
- Street light ON/OFF timing information.
- Relevant system status messages.

### Step 7: Interrupt-Driven Configuration

A dedicated push button generates an EINT0 interrupt request.

The interrupt mechanism requests entry into the configuration menu.

The menu allows the user to modify:

- Hour.
- Minute.
- Second.
- Day.
- Month.
- Year.
- Street light ON Time.
- Street light OFF Time.

The keypad is used to enter the required values.

The menu validates the input before accepting the configuration.

---

## 8. Street Light Decision Table

| ADC Valid | LDR Initialized | Schedule Active | Darkness Detected | Output |
|---|---|---|---|---|
| Yes | Yes | Yes | Yes | ON |
| Yes | Yes | Yes | No | OFF |
| Yes | Yes | No | Yes | OFF |
| Yes | Yes | No | No | OFF |
| No | Yes | Yes | Yes | OFF |
| Yes | No | Yes | Yes | OFF |
| No | No | No | No | OFF |

The street lights are activated only when all four required conditions are satisfied.

---

## 9. Default Operating Schedule

The current street light control module contains the following default configuration:

| Parameter | Default Value |
|---|---|
| Start Hour | 18 |
| Start Minute | 30 |
| End Hour | 6 |
| End Minute | 0 |
| ON Time | 18:30 |
| OFF Time | 06:00 |

The schedule can be modified through the configuration menu.

The schedule variables are maintained in application memory. Persistent storage across complete power loss is not claimed in this implementation.

---

## 10. ADC and LDR Operation

The LDR is a light-sensitive resistor whose resistance changes according to the surrounding illumination.

- Higher illumination generally reduces LDR resistance.
- Lower illumination generally increases LDR resistance.

The ADC converts the analog voltage generated by the sensor circuit into a digital value.

The LDR driver evaluates this digital measurement and determines the darkness condition.

The discussed implementation uses configurable hysteresis thresholds of 650 and 550 ADC counts.

Hysteresis helps prevent rapid switching when the measured illumination fluctuates near the threshold.

The actual dark/bright polarity depends on the LDR voltage-divider connection.

---

## 11. Software Architecture

The project follows a modular embedded software architecture.

Each peripheral has its own driver implementation and header interface.

```text
                 +----------------------+
                 |       main.c         |
                 |  Main Application    |
                 +----------+-----------+
                            |
          +-----------------+------------------+
          |                 |                  |
          v                 v                  v
    +-----------+     +-----------+      +-----------+
    |   RTC     |     |    LDR    |      |    LCD    |
    |  Driver   |     |  ADC      |      |  Driver   |
    +-----------+     +-----------+      +-----------+
          |                 |                  |
          +-----------------+------------------+
                            |
                            v
                  +--------------------+
                  | Street Light      |
                  | Decision Module   |
                  +--------------------+
                            |
                            v
                  +--------------------+
                  | Street Light GPIO  |
                  | Outputs            |
                  +--------------------+

        User Configuration Interface

        +-------------+       +-------------+
        | 4x4 Keypad  | ----> | Menu Module |
        +-------------+       +-------------+
                                    ^
                                    |
                             +-------------+
                             |   EINT0     |
                             | Interrupt   |
                             +-------------+
```

---

## 12. Repository Structure

The repository is organized into source files, header files and project documentation.

```text
Adaptive-Street-Light-Control/
│
├── main.c
│
├── rtc.c
├── rtc.h
│
├── ldr.c
├── ldr.h
│
├── lcd.c
├── lcd.h
│
├── kpm.c
├── kpm.h
│
├── eint.c
├── eint.h
│
├── menu.c
├── menu.h
│
├── streetlight.c
├── streetlight.h
│
├── delay.c
├── delay.h
│
├── types.h
├── project_config.h
│
├── README.md
│
└── images/
    ├── adaptive_street_light_hardware_setup.jpg
    ├── lcd_display.jpg
    └── project_working.jpg
```

**Note:** This is the logical module organization. Keep the actual filenames and capitalization consistent with the source files in the repository. Include only files that belong to your final project version.

---

## 13. Development Environment and Build Procedure

### Development Environment

- Microcontroller: LPC2148.
- Architecture: ARM7TDMI-S.
- Programming Language: Embedded C.
- IDE: Keil µVision.
- Compiler: ARM C Compiler supported by the selected Keil toolchain.
- Programming Utility: Flash Magic.

### Steps to Build the Project

1. Install Keil µVision with the appropriate ARM7 toolchain.
2. Create or open the LPC2148 target project.
3. Add all required `.c` source files to the project.
4. Include the corresponding `.h` header files.
5. Configure the target device as LPC2148.
6. Configure the required clock and compiler settings.
7. Ensure all required source files are included in the build.
8. Compile the project.
9. Resolve any compilation or linking errors.
10. Generate the HEX output file.

### Steps to Program the Microcontroller

1. Connect the LPC2148 development board.
2. Connect the board to the computer using the supported programming interface.
3. Open Flash Magic.
4. Select the appropriate LPC2148 device and communication settings.
5. Load the generated HEX file.
6. Execute the programming operation.
7. Reset the microcontroller.
8. Observe the system operation on the hardware.

**Important:** The actual clock, baud rate, oscillator and programming-interface settings must match your hardware configuration.

---

## 14. Testing and Verification

The following test cases can be used to verify the system.

| Test Case | Input / Condition | Expected Result |
|---|---|---|
| TC01 | System power ON | Drivers initialize |
| TC02 | Current time before ON time | Lights OFF |
| TC03 | Current time reaches ON time and environment is dark | Lights ON |
| TC04 | Environment becomes bright | Lights OFF |
| TC05 | Current time reaches OFF time | Lights OFF |
| TC06 | Overnight schedule | Correct midnight-crossing operation |
| TC07 | Equal ON and OFF timings | Schedule disabled |
| TC08 | EINT0 button pressed | Configuration menu requested |
| TC09 | Valid keypad input | Configuration value accepted |
| TC10 | Invalid RTC value | Input rejected |
| TC11 | Invalid ADC measurement | Lights OFF |
| TC12 | LDR driver not initialized | Lights OFF |
| TC13 | Schedule changed through menu | Updated schedule applied |

---

## 15. Advantages

- Automatic street light operation.
- RTC-based configurable scheduling.
- Environmental illumination-based control.
- Reduced unnecessary energy consumption.
- User-friendly LCD and keypad interface.
- Interrupt-driven configuration.
- Supports overnight operating schedules.
- Modular and maintainable Embedded C implementation.
- Independent peripheral driver architecture.

---

## 16. Applications

- Smart street lighting systems.
- Residential street lighting.
- Campus and university lighting.
- Industrial premises.
- Parking area illumination.
- Roadside lighting automation.
- Smart city infrastructure.

---

## 17. Limitations and Future Enhancements

The current project provides RTC scheduling and LDR-based adaptive street light control.

Possible future enhancements include:

- Wireless monitoring and remote configuration.
- IoT-based street light monitoring.
- Centralized control of multiple street light units.
- Fault detection and maintenance alerts.
- Energy consumption monitoring.
- Battery-backed RTC operation.
- Data logging and historical operating statistics.
- Advanced scheduling based on seasonal changes.

These are possible enhancements and are not claimed as implemented features of the current version.

---

## 18. Important Implementation Notes

- The project is designed around the LPC2148 microcontroller.
- The application is developed using Embedded C.
- Peripheral operations are separated into independent driver modules.
- The RTC schedule supports both normal and overnight time intervals.
- Street light activation requires both an active schedule and detected darkness.
- ADC failure defaults to lights OFF in the current implementation.
- The RTC and schedule configuration should be validated before being applied.
- GPIO pin assignments must match the actual circuit connections.
- No separate EEPROM/EPROM driver is included in this documented project version.
- The generated HEX file is programmed into the microcontroller using Flash Magic.

---

## 19. Conclusion

The Adaptive Street Light Control Using Time and Environmental Illumination project demonstrates the practical implementation of embedded systems using the LPC2148 ARM7 microcontroller.

By integrating RTC-based scheduling with LDR-based ambient light detection, the system provides an intelligent approach to automatic street light management.

The keypad-based configuration interface and interrupt-driven menu improve operational flexibility by allowing users to modify RTC parameters and street light operating timings without changing the application source code.

The modular software architecture also demonstrates the application of Embedded C programming concepts, peripheral interfacing, GPIO control, ADC operation, interrupt handling, and real-time clock management.

This project provides a foundation for developing more advanced intelligent lighting and smart city automation systems.

---

## 20. Author and Project Information

**Project Title:** Adaptive Street Light Control Using Time and Environmental Illumination

**Controller:** LPC2148 ARM7

**Programming Language:** Embedded C

**Development Environment:** Keil µVision

**Programming Tool:** Flash Magic

**Project Domain:** Embedded Systems and Automation

**Repository:** Adaptive Street Light Control

---

⭐ If you find this project useful, consider giving the repository a star on GitHub.

