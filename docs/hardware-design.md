# Hardware design

## Functional overview

The board routes a 3S LiPo input through a transient suppressor and an electronic fuse, then converts the protected battery rail to 3.3 V. The STM32G474 reads the sensors, monitors power, controls the external watchdog, communicates over CAN-FD and UART, and accesses external serial flash for data storage.

The inertial sensors, pressure sensor, MCU, CAN interface, watchdog, and flash share a common ground. Sensor supply branches use ferrite beads and local capacitors for filtering; they are not independent regulators or isolated domains.

## Power path

1. J1 provides VBAT_RAW and ground. D2 (SMAJ13A) is connected across the input for transient suppression.
2. U8 (TPS25940A) provides eFuse functions including undervoltage and overvoltage monitoring, current limiting, controlled output rise, fault indication, and analog current monitoring.
3. VBAT_PROTECTED feeds U9 and is also routed to J2 pin 1.
4. U9 (TPS62902) and L1 generate 3V3_MAIN.
5. FB1 and FB2 with local capacitance form filtered 3V3_SENS and 3V3_BARO branches. FB3 optionally filters the MCU VDDA branch.

With R22 = 442 kΩ, R23 = 20.5 kΩ, and R24 = 36.5 kΩ, nominal resistor-based thresholds are approximately:

| Parameter | Nominal calculation | Result |
|---|---|---:|
| Undervoltage rising | 0.99 × 499 / 57 | 8.67 V |
| Undervoltage falling | 0.93 × 499 / 57 | 8.14 V |
| Overvoltage cutoff | 0.99 × 499 / 36.5 | 13.53 V |
| eFuse current limit | 89 / 51.1, using R25 = 51.1 kΩ | 1.74 A |

These are nominal calculations using the device's typical thresholds and selected resistor values. They are not measurements or cell-level battery protection limits. R32 = 150 kΩ and R33 = 27.4 kΩ divide VBAT_PROTECTED to the MCU ADC. The eFuse current-monitor output is converted through R26 = 18.7 kΩ and routed to an ADC input.

## Processing and sensors

- U1, STM32G474RET6, is the central controller.
- U3, BMI088, provides accelerometer and gyroscope data over SPI1 with separate chip-select and data-ready signals.
- U4, ADXL375, provides high-g acceleration data over SPI2; its specified measurement range is ±200 g.
- U5, LPS22DF, measures absolute pressure over I2C3. Application firmware can use pressure readings to estimate relative altitude.
- U6, W25Q128JV, provides 128 Mbit (16 MiB) of SPI NOR storage over SPI3.
- Y1 and Y2 provide 8 MHz and 32.768 kHz clock references.
- U2, TPS3431, is an external watchdog connected to the MCU watchdog signals and reset net. Firmware must service and configure the watchdog appropriately.

## Communications

U7, TCAN3413, connects the MCU FDCAN1 peripheral to the CAN bus. U10 provides transient protection at the CAN connector. R19 and R20 are fitted 0 Ω links between the transceiver and bus. R21 is a 120 Ω termination resistor; JP1 controls whether the termination is connected. The CAN bus is not isolated.

J3 provides 3.3 V UART through USART2. J4 carries the custom SWD pinout described in [Programming and debug](programming.md).

## PCB and mechanical design

The board outline is 50 × 80 mm with four copper layers and nominal 1.6 mm thickness. Four 3.2 mm non-plated mounting holes use a 40 × 70 mm rectangular pattern. The long axis follows the +X nose direction marked on the board. The pressure-sensor port is identified on the silkscreen and should remain open to ambient pressure.

There are 29 test pads on the bottom side. Their signal groups are listed in [Connector pinout](pinout.md).

## Electrical boundaries

The board does not charge or balance a LiPo pack and does not measure individual cell voltages. The eFuse undervoltage threshold is for pack-level input control and should not be treated as per-cell battery protection. The design has no guaranteed reverse-battery protection. The SMAJ13A TVS clamp can exceed the eFuse absolute-maximum input voltage under some surge conditions, so the source, harness, and transient exposure must be suitable for the selected protection network.