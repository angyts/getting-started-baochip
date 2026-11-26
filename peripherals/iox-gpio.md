# IOX, GPIO and pin multiplexing

**In one sentence:** IOX is the system that decides what each physical chip pin is connected to, while GPIO is the basic way software reads or drives a pin as a digital input/output.

## Why does pin multiplexing exist?

The Baochip has more possible hardware functions than it has physical pins. A single pad may be used for ordinary GPIO or assigned to one of several alternate functions, such as a UART, SPI, I²C or BIO signal.

This is called *pin multiplexing* (or pin muxing).

Think of a physical pin as a multi-purpose socket. The pin mux is the switch that connects that socket to one internal function at a time. Selecting the right peripheral in software is not enough if the physical pad is still connected to a different function.

## The main choices

The documentation describes four primary pad functions:
- GPIO (function 0).
- AF1.
- AF2.
- AF3.

BIO is selected as a further choice within AF1 through the BIOSEL register. If a BIO bit is selected, BIO overrides the normal GPIO/AF output control for that pad.

## BIO pin numbering

The BIO pins map to ports B and C:

| BIO bit | Physical chip pin |
|---:|---|
| 0–15 | PB0–PB15 |
| 16–31 | PC0–PC15 |

For the BaoBAB audio experiment:
- PC8 = BIO24.
- PC10 = BIO26.
- PC11 = BIO27.

This maps the **chip pins**. It does not tell you which Dabao header pad exposes each signal; use the Dabao board schematic/pinout for that.

## GPIO basics

A GPIO pin can be configured as:
- Input or output.
- Push-pull or open-drain output.
- Floating input or input with pull-up/down.
- With configurable Schmitt-trigger input behaviour.
- With configurable output drive strength and slew-rate settings.

The output data register chooses HIGH or LOW. The output-enable register chooses whether a pin is driving an output. The input register reports the pin's current input state.

The IOX documentation says the GPIO output drive choices are 2 mA, 4 mA, 8 mA and 12 mA. These are drive-strength settings, not a promise that a board pin can continuously supply that current to an arbitrary load. Check electrical limits and the board's power design.

## GPIO interrupts

All ports support external interrupt capability. The pin must be configured as an input. The interrupt can trigger on:
- Rising edge.
- Falling edge.
- High level.
- Low level.

There are eight configurable GPIO interrupt channels. They can also be configured for wake-up behaviour.

## Electrical configuration

The source identifies registers for:
- Pull-ups (`GPIOPU`).
- Slew-rate control.
- Drive strength (`GPIOCFG_DRVSEL`).
- Schmitt-trigger input enable (`GPIOCFG_SCHM`).
- Alternate-function selection (`AFSEL`).
- BIO selection (`BIOSEL`).

These settings affect electrical behaviour at the pad. Do not blindly raise drive strength or disable filtering to solve a signal problem; first understand the line, its load, voltage and required edge speed.

## A subtle keypad interaction

The documentation warns that the always-on keypad controller can override GPIO settings on Port F. Some PF pins are tied to PA pins, so an unexpected combination can cause contention or excessive power during sleep. Pin conflicts must be checked as a group, not just one label at a time.

## Where to read more

- [Official IO configuration chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/ch03-00-io-configuration.md)
- [IOX register group](https://ci.betrusted.io/bao1x/iox.html)
- [Hardware pinout chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/ch01-00-electrical-specifications.md)
- [Dabao board design](https://github.com/baochip/dabao)

