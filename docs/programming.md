# Programming and debug

The STM32G474 is programmed over SWD through J4. The external W25Q128 serial NOR is intended for application data storage; it is not the MCU's program memory.

## J4 to STLINK-V3MINIE STDC14

| J4 pin | Board signal | STDC14 pin |
|---:|---|---:|
| 1 | 3V3_MAIN / target reference | 3, T_VCC |
| 2 | SWDIO | 4 |
| 3 | GND | 5 |
| 4 | SWCLK | 6 |
| 5 | GND | 7 |
| 6 | NRST | 12 |

This table maps electrical contacts, not cable colors. Verify the board connector orientation and continuity through the complete adapter before connecting a probe. J4 is not wired to the standard TC2030-CTX pinout.

## Connection sequence

1. With the board unpowered, connect the debugger to J4 using an adapter wired for the pin mapping above.
2. Power the board separately through J1. Use a supply appropriate for the board input range and observe current during initial power-up.
3. Connect with STM32CubeProgrammer using ST-LINK and SWD.
4. Program the STM32G474 application image, verify the write, and reset the board.

J4 pin 1 is a target-voltage reference from the board. It is not a battery input. Do not connect battery voltage to the debugger's VTref contact. J3 is a separate 3.3 V UART interface and is not RS-232.

## References

- [STLINK-V3MINIE user manual](https://www.st.com/resource/en/user_manual/um2910-stlinkv3minie-evaluation-board-stmicroelectronics.pdf)
- [TC2030-IDC-NL](https://www.tag-connect.com/product/tc2030-idc-nl)
- [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html)