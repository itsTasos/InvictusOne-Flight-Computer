# Connector pinout and test points

## External connectors

| Connector | Pin | Signal | Notes |
|---|---:|---|---|
| J1 — battery | 1 | VBAT_RAW | Battery positive, 9.0–12.6 V for a 3S LiPo |
|  | 2 | GND | Common board ground |
| J2 — CAN module bus | 1 | VBAT_PROTECTED | Protected battery voltage; not 3.3 V |
|  | 2 | GND | Common board ground |
|  | 3 | CAN_H | CAN-FD bus high |
|  | 4 | CAN_L | CAN-FD bus low |
| J3 — UART | 1 | 3V3_MAIN | 3.3 V output |
|  | 2 | GND | Common board ground |
|  | 3 | UART_TX | MCU transmit |
|  | 4 | UART_RX | MCU receive |
| J4 — SWD | 1 | 3V3_MAIN | Debugger target-voltage reference |
|  | 2 | SWDIO | SWD data |
|  | 3 | GND | Common board ground |
|  | 4 | SWCLK | SWD clock |
|  | 5 | GND | Common board ground |
|  | 6 | NRST | MCU reset |

J3 is 3.3 V UART, not RS-232. J4 is a custom pin assignment and does not follow the standard TC2030-CTX mapping. Confirm the connector orientation and pin numbering in the KiCad design before making a cable.

## MCU peripheral allocation

| Peripheral | Connected devices or interface |
|---|---|
| SPI1 | BMI088 |
| SPI2 | ADXL375 |
| SPI3 | W25Q128 serial flash |
| I2C3 | LPS22DF pressure sensor, 7-bit address 0x5C |
| FDCAN1 | TCAN3413 CAN-FD transceiver |
| USART2 | J3 UART connector |
| SWD | J4 debug connector |

The BMI088 accelerometer and gyroscope use separate chip-select signals on SPI1. The ADXL375 has two interrupt outputs. The pressure sensor has an interrupt output. The external watchdog is connected to the MCU reset network.

## Bottom-side test points

| Test point | Signal | Test point | Signal |
|---|---|---|---|
| TP1 | NRST | TP16 | FLASH_CS |
| TP2 | WDG_WDI | TP17 | CAN_H |
| TP3 | WDG_WDO / NRST | TP18 | CAN_L |
| TP4 | SPI1_SCK | TP19 | VBAT_RAW |
| TP5 | SPI1_MISO | TP20 | VBAT_PROTECTED |
| TP6 | SPI1_MOSI | TP21 | 3V3_MAIN |
| TP7 | SPI2_SCK | TP22 | 3V3_SENS |
| TP8 | SPI2_MISO | TP23 | 3V3_BARO |
| TP9 | SPI2_MOSI | TP24 | UART_TX |
| TP10 | I2C3_SCL | TP25 | UART_RX |
| TP11 | I2C3_SDA | TP26 | EFUSE_IMON |
| TP12 | BARO_INT | TP27 | VBAT_SENSE_ADC |
| TP13 | SPI3_SCK | TP28 | EFUSE_FLT |
| TP14 | SPI3_MISO | TP29 | BUCK_PGOOD |
| TP15 | SPI3_MOSI |  |  |

There is no dedicated ground test point; use a verified ground contact such as J1 pin 2. TP3 is labeled for watchdog output but is electrically on the NRST net.