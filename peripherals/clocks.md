# Clock generation — the chip's timing system

**In one sentence:** clock-generation hardware creates and distributes the timing signals that make the CPU, memory, buses and peripherals operate at the correct speeds.

A chip does not have one single clock for everything. The Baochip has several clock domains, each with different performance and power requirements.

## The clock hierarchy

The clock chapter lists these maximum frequencies:

| Clock | Maximum | Plain-English role |
|---|---:|---|
| FCLK | 700 MHz | Fastest domain; BIO uses this clock |
| ACLK | 350 MHz | AXI / QFC / SRAM / Vex CPU domain |
| HCLK | 175 MHz | DMA / SCE / AHB domain |
| ICLK | 87.5 MHz | Interface domain |
| PCLK | 43.75 MHz | Peripheral bus domain |
| AOCLK | 2 MHz | Always-on domain |
| PKECLK | 175 MHz | Public-key engine clock |

The CPU is not the fastest clock domain. The documentation explicitly notes that BIO uses FCLK, while the CPU runs from a divided clock.

## Sources and dividers

The book lists two external oscillators (48 MHz and 32.768 kHz) and two internal oscillators (32 MHz and 32 kHz), plus two PLLs.

The Clock Generation Unit (CGU) selects sources and divides frequencies. A programmed divider changes a source clock into a slower clock for a particular subsystem.

The book also describes a frequency-sensor mechanism that can measure FCLK, the oscillators and PLL outputs. A validity register indicates whether the result should be treated as valid.

## Why developers should care

Clock settings affect:
- How quickly the CPU executes.
- How fast buses and DMA engines operate.
- Whether a peripheral can reach its required bitrate.
- Power consumption and thermal behaviour.
- Timing-sensitive BIO programs.

A requested BIO quantum frequency is not automatically the frequency of a signal on a pin. The BIO instruction sequence between pin transitions also affects the final waveform.

## Clock cipher caution

The CGU includes an optional clock-cipher function that skips clock pulses according to an LFSR. The book says this can harden against side-channel analysis at a performance cost. It also says this feature has not been thoroughly characterised and is not recommended for CLKTOP in some settings because instability has been observed. Do not enable it casually.

## Documentation caveat

The clock chapter explicitly notes that parts of the clock subsystem remain unvalidated or less well characterised. If a clock configuration is security-, timing- or hardware-critical, check the RTL and measure the resulting clock.

## References

- [Official clock-generation chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/clock-generation.md)
- [Clock register group and other register references](https://ci.betrusted.io/bao1x/)
- [System-control RTL](https://github.com/baochip/baochip-1x/tree/main/rtl/modules/sysctrl/rtl)

