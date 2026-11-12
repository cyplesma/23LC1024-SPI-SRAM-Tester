# 23LC1024 SPI SRAM Tester

An Arduino Uno R3 bench tester for 23LC1024 serial SRAM chips. The sketches check SPI communication, write and verify the full 128 KiB address space, and support repeated test passes. The KiCad design is a three-socket test board for extending the single-chip bench setup.

## Project status

| Item | Status |
| --- | --- |
| Single-chip test on Arduino Uno R3 | Full-memory verification passed at 125 kHz, 1 MHz, 4 MHz, and 8 MHz |
| Repeated testing at 8 MHz | 37 passes observed without a reported error |
| Three-socket KiCad board | PCB layout with a bottom ground plane. Operation with all three sockets populated is pending verification. |

## Repository layout

| Path | Contents |
| --- | --- |
| `README.md` | Project overview, test procedure, and bench results |
| `hardware/` | KiCad project (`.kicad_pro`), schematic (`.kicad_sch`), PCB layout (`.kicad_pcb`), and any project-specific libraries |
| `firmware/` | Arduino sketches, each in a folder with the same name as its main `.ino` file |
| `docs/` | Optional schematic PDF, board photos, and Serial Monitor logs |

## Firmware

| Sketch | Purpose |
| --- | --- |
| 96-byte diagnostic | Exercises three access methods over a small memory range and checks the mode register |
| Full-memory test | Writes and verifies 131,072 bytes in 32-byte blocks using `0x00`, `0xFF`, `0x55`, `0xAA`, and an address-derived pattern |
| Continuous SPI clock test | Generates ongoing SPI activity for probing the clock signal |

## Running a test

1. Start with one SRAM chip. Check the chip orientation, power, common ground, and SPI connections. Use the KiCad schematic for board wiring and confirm that the sketch's chip-select setting matches the socket under test.
2. Open the 96-byte diagnostic in the Arduino IDE, select **Arduino Uno** and the correct serial port, and upload it. Set Serial Monitor to the baud rate in the sketch's `Serial.begin()` call.
3. Start with the bench diagnostic settings of SPI mode 3, 125 kHz, and a 5 µs chip-select delay. The bench diagnostic reported `0x40` for sequential mode and passed all three access checks.
4. Upload the full-memory sketch and verify that all five patterns pass. Repeat at higher clock rates if needed, recording the board revision, socket, SPI clock, and pass count for each run.

The memory tests overwrite the addresses they exercise.

## Bench notes

- The first diagnostic read `0x00` from the mode register. Reseating the chip restored the expected `0x40` reading.
- Intermittent data mismatches stopped after the oscilloscope ground clip was removed and the Uno and SRAM common ground was confirmed.
- The single-chip bench results came from jumper-wire testing. The PCB still needs its own run with all three sockets populated.

<!-- Maintainer checklist
- Keep the KiCad project, schematic, PCB layout, and referenced custom libraries together under hardware/.
- Use project-relative paths for any custom libraries and check that the project opens from a fresh clone.
- Put each actual .ino file in its own matching sketch folder under firmware/.
- Add photos or serial logs under docs/ and link them here if desired.
- Update the three-socket status after testing each socket with all three chips populated.
- Add the license files you choose for the hardware and firmware before publishing.
-->
