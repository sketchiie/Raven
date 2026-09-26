# Raven

A compact, crow-compatible Eurorack module built on the RP2040. It is a stripped-down derivative of the [Music Thing Modular Workshop Computer](https://github.com/TomWhitwell/Workshop_Computer), designed to do one job: run [Blackbird](https://github.com/TomWhitwell/Workshop_Computer/tree/main/releases/41_blackbird), unmodified.

Plug it into a computer or a [norns](https://monome.org/docs/norns/) over USB and it behaves like [monome crow](https://monome.org/docs/crow/): live-code it from druid (or [web-druid](https://dessertplanet.co/web-druid/)), run crow scripts from norns, drive it from Max/MSP, or upload a Lua script to run standalone.

> **This repository currently contains the schematic only.** PCB layout, front panel and calibration tooling will follow once they've been proven on more boards.

## What it is

The Workshop Computer is a general-purpose module with knobs, a switch, LEDs and a dozen jacks, and program cards that change what it does. Blackbird is one of those cards, and it turns the Workshop Computer into a crow. Raven keeps only the hardware Blackbird needs for crow's core I/O, and drops the rest to make a smaller, cheaper, single-purpose module.

| Jack              | crow        | Circuit                                     |
| ----------------- | ----------- | ------------------------------------------- |
| CV In 1           | `input[1]`  | Via CD4052 mux to ADC (GP29)                |
| CV In 2           | `input[2]`  | Via CD4052 mux to ADC (GP29)                |
| CV Out 1          | `output[1]` | PWM (GP23), filtered and scaled, calibrated |
| CV Out 2          | `output[2]` | PWM (GP22), filtered and scaled, calibrated |
| Audio Out 1 (CV3) | `output[3]` | MCP4822 channel A                           |
| Audio Out 2 (CV4) | `output[4]` | MCP4822 channel B                           |

Plus USB-C for the crow serial interface, an EEPROM for calibration data, and BOOTSEL and RESET buttons.

## What it isn't

A crow.

Unfortunately if you're after the ability to link it up to the ii/i2c ecosystem you're out of luck. There is no i2c header, and as far as I can tell the RP2040 can't act as both an i2c host and a listener so even with an i2c header it wouldn't be possible to fully replicate the original crow's functionality.

The same limitation on the Workshop Computer also applies here causing CV 3 and 4 (originally audio out 1 and 2) to not be perfectly v/oct accurate. They are pretty close though.

### How it differs from the Workshop Computer

- **No knobs, switch, LEDs, pulse I/O or audio inputs.** Only the six jacks crow's core I/O needs are kept.
- **The analog mux stays.** CV In 1 and 2 are not wired to ADC pins directly. They reach the RP2040 through the 4052, exactly as on the Workshop Computer, so the stock ComputerCard/Blackbird firmware reads them without modification.
- **Pin-compatible with Blackbird.** Every signal Raven keeps is on the same RP2040 pin as on the Workshop Computer, so no firmware fork is needed.
- **Calibration header.** A 2×10, pogo pad array carries SWD, UART, all four outputs, both CV inputs, and a separate ground-sense line. It lets a board be flashed and calibrated without panel controls, which the stock calibration routine relies on.

## Status

The design has been fabricated and assembled. Boards have been flashed with Blackbird, calibrated, and come up as a crow in web-druid.

This is a hobby project shared as-is. It has not been through any formal compliance testing. Build at your own risk.

## Credits and thanks

Raven would not exist without other people's open work, and it borrows heavily from it.

**[Music Thing Modular Workshop Computer](https://github.com/TomWhitwell/Workshop_Computer)**
by Tom Whitwell and contributors. Raven's core circuit: RP2040 and its support parts, the CV input and output stages, the MCP4822 audio outputs, the 4052 mux and the calibration EEPROM all follow the Workshop Computer's published design. Thanks for making the Workshop Computer open, and for building a platform other people can build on.

**[Blackbird](https://github.com/TomWhitwell/Workshop_Computer/tree/main/releases/41_blackbird)**
by Dune Desormeaux ([@dessertplanet](https://github.com/dessertplanet)). The firmware this whole module exists to run.

**[crow](https://monome.org/docs/crow/)** by monome. The protocol, scripting model and ecosystem Blackbird implements.

Raven is an independent project. It is not affiliated with or endorsed by Music Thing Modular, monome, or the authors above. Any mistakes in it are mine.

Blackbird firmware is not included in this repository. Get it from the [Workshop Computer releases](https://github.com/TomWhitwell/Workshop_Computer/tree/main/releases/41_blackbird)
