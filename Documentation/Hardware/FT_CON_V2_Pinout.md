# FT_CON V2 — Hardware Pinout Reference

**Source:** `1-Schematic_FT_CON_V2_ESP32_(PROD).json`  
**Board:** FT_CON V2  
**MCU module:** ESP32-S3-WROOM-1U-N4  
**Motor drivers:** 2 × TI DRV8833CPWP  
**Status:** Initial schematic-derived reference; verify against the manufactured PCB before connecting external hardware.

---

## 1. Motor driver architecture

V2 contains two DRV8833 dual H-bridge drivers, providing four independent brushed DC motor channels.

| Motor | Driver | Driver input | ESP32 signal | ESP32 GPIO | Driver output nets |
|---|---|---|---|---:|---|
| M1 | U2 | AIN1 | GP01 | GPIO1 | M1A / M1B |
| M1 | U2 | AIN2 | GP02 | GPIO2 | M1A / M1B |
| M2 | U2 | BIN1 | GP41 | GPIO41 | M2A / M2B |
| M2 | U2 | BIN2 | GP42 | GPIO42 | M2A / M2B |
| M3 | U3 | AIN1 | GP37 | GPIO37 | M3A / M3B |
| M3 | U3 | AIN2 | GP38 | GPIO38 | M3A / M3B |
| M4 | U3 | BIN1 | GP40 | GPIO40 | M4A / M4B |
| M4 | U3 | BIN2 | GP39 | GPIO39 | M4A / M4B |

The A/B output nets are the two terminals of each motor channel. Motor direction and speed are controlled by the corresponding pair of driver input signals.

### Driver enable (`nSLEEP`)

- U2 `nSLEEP` is labelled `STBY1`.
- U3 `nSLEEP` is labelled `STBY2`.
- Neither signal is assigned to an ESP32 GPIO in the schematic.
- The intended board usage is for the user to enable each driver through an external pull-up connection, rather than consuming MCU GPIOs.

**Electrical recommendation to validate in the hardware design:** use a pull-up resistor in the 20–75 kΩ range, as recommended by Texas Instruments for `nSLEEP`. A 47 kΩ resistor is a suitable nominal choice. The proposed pull-up rail is 5 V, not an ESP32 GPIO. Confirm the actual rail and wiring on the PCB before applying power. TI documents a 6.5 V clamp on `nSLEEP` and recommends the resistor to limit clamp current if the pull-up is taken from `VM`. Do not connect `nSLEEP` directly to a 12 V rail.

The driver requires up to 1 ms to become fully operational after leaving sleep mode.

### Driver signals visible in the schematic

- `nFAULT` pins are not shown connected to ESP32 GPIOs.
- `AISEN` and `BISEN` are the current-sense inputs; the schematic should be checked at the PCB level before assuming current measurement is implemented.
- Both drivers' motor supply pins use the `MP` net.

---

## 2. I²C

| Signal | Schematic net | ESP32 GPIO |
|---|---|---:|
| SDA | `I2C_SDA` | GPIO36 |
| SCL | `I2C_SCL` | GPIO35 |

These signals are also labelled on the GPIO breakout.

---

## 3. UART

| Signal | Schematic net | ESP32 GPIO |
|---|---|---:|
| TX | `TX` | GPIO43 (module TXD0) |
| RX | `RX` | GPIO44 (module RXD0) |

The UART signals are routed to the relevant breakout/header labels. Confirm the intended connector orientation and pin order against the PCB before wiring an external device.

---

## 4. Boot and reset

| Function | Schematic net | ESP32 connection |
|---|---|---|
| Boot button | `BOOT` | GPIO0 |
| Reset | `EN` | ESP32 EN |

GPIO0 is a strapping pin and should not be treated as an unrestricted application GPIO.

---

## 5. GPIO breakout

The schematic uses `GPxx` labels for GPIO signals. The corresponding ESP32 GPIO numbers are the numeric suffixes.

GPIO signals labelled in the schematic include:

`GP01`, `GP02`, `GP03`, `GP04`, `GP05`, `GP06`, `GP07`, `GP08`, `GP09`, `GP10`, `GP11`, `GP12`, `GP13`, `GP14`, `GP15`, `GP16`, `GP17`, `GP18`, `GP21`, `GP37`, `GP38`, `GP39`, `GP40`, `GP41`, `GP42`, `GP45`, `GP47`, `GP48`.

Not every GPIO is necessarily available for unrestricted use: some are assigned to the motor drivers, boot/reset, I²C, UART, USB, or other onboard circuitry. Firmware must use the board-specific allocation rather than assume every breakout label is free.

---

## 6. Power and common signals

Schematic net labels include:

- `PWR_IN`
- `MP` — motor-driver supply net
- `5V0`
- `USB_5V`
- `3V3`
- `GND`

Do not assume `MP` is always 12 V solely from the presence of a 12 V input connector. Verify the actual power path, regulator arrangement, and voltage limits on the complete schematic and PCB before connecting motors.

---

## 7. Firmware allocation guidance

The firmware's V2 board definition should reserve these signals:

| Subsystem | Reserved GPIOs |
|---|---|
| Motor 1 | GPIO1, GPIO2 |
| Motor 2 | GPIO41, GPIO42 |
| Motor 3 | GPIO37, GPIO38 |
| Motor 4 | GPIO40, GPIO39 |
| I²C | GPIO35, GPIO36 |
| UART | GPIO43, GPIO44 |
| Boot | GPIO0 |
| Reset | EN (not a normal GPIO assignment) |

`STBY1` and `STBY2` are **not** MCU GPIO assignments.

The application layer should be able to disable motor functionality entirely. Applications that do not use the motor drivers can leave the drivers disabled through the external `nSLEEP` wiring and use the remaining available board resources.

---

## 8. Items to verify before firmware pin definitions are frozen

1. Confirm the exact `MP` supply voltage and its maximum possible value.
2. Confirm the intended 5 V pull-up arrangement for `STBY1` and `STBY2`, including whether the pull-up resistors are fitted on the PCB or supplied externally.
3. Confirm the PCB connector pin order for all four motor outputs and the GPIO breakout.
4. Confirm the actual PCB routing of `nFAULT`, `AISEN`, and `BISEN`.
5. Cross-check the schematic-derived pinout against the PCB layout and a continuity test on the assembled board.

---

## 9. Datasheet reference

Texas Instruments, **DRV8833 Dual H-Bridge Motor Driver**, datasheet SLVSAR1E. Section 7.3.4 describes `nSLEEP`, its internal pull-down and clamp, the recommended 20–75 kΩ pull-up resistor, and wake-up time.

https://www.ti.com/lit/ds/symlink/drv8833.pdf
