# BIO — Bao I/O coprocessor

**In one sentence:** BIO is a set of tiny programmable processors inside the Baochip that can perform precise input/output work without asking the main CPU to toggle every pin in software.

For example, a normal application can ask BIO to send a sequence of bits to an LED strip or to implement a timing-sensitive serial protocol. The BIO cores then execute that small program independently.

## Why does the chip have BIO?

Some electronic protocols care about timing down to very small fractions of a microsecond. If the main CPU has to do every step, interrupts, scheduling and other work can disturb the exact timing.

BIO is designed for this kind of job. It gives a small processor a dedicated job and lets it wait for precise timing events.

The official book describes BIO as a PicoRV32-based RV32E core with compressed instructions and a hardware multiplier. It has four cores. The normal register bank is deliberately small; special high-numbered registers are repurposed as FIFOs, GPIO access and synchronisation primitives.

## The special registers, in normal language

| Register | Meaning in plain English |
|---|---|
| x16–x19 | Four FIFO queues. Reading removes a word; writing adds a word. |
| x20 | Wait until the next configured timing “quantum” (or external clock event). |
| x21 | Read GPIO pad inputs, or write a masked set of pin values. |
| x22 | Set selected GPIO outputs HIGH. |
| x23 | Clear selected GPIO outputs LOW. This register has unusual clear-on-zero semantics. |
| x24 | Configure selected GPIO pins as outputs. |
| x25 | Configure selected GPIO pins as inputs. |
| x26 | GPIO mask: tells the other GPIO registers which pins they can affect. |
| x27–x30 | Event mask, set, clear, and wait/read event status. |
| x31 | Core identity and a count of core clock cycles since reset. |

One important trap: x23 is not an ordinary “write 1 to clear” register. BIO GPIO clear uses **clear-on-zero** semantics. The source documentation says this unusual rule makes it easier to shift serial data onto pins.

## What is a quantum?

A quantum is a timing checkpoint. When BIO executes an instruction that accesses x20, the BIO core pauses until the next configured quantum occurs.

Think of it as a metronome tick:
- BIO executes a few instructions.
- It reaches x20 and waits.
- The next tick arrives.
- BIO continues.

The quantum can come from an internal clock divider or an external GPIO clock source. This can simplify timing-sensitive code, provided the quantum is longer than the longest instruction path between waits.

**Important:** the quantum frequency is not automatically the frequency of a pin's output. The actual signal frequency depends on how many instructions and waits the BIO program executes between pin transitions.

## FIFOs: how the main CPU and BIO exchange data

A FIFO is a first-in, first-out queue. The host CPU can place data into a FIFO; the BIO core can read it later. BIO can also return data through the queues.

The documentation describes each FIFO as eight-deep storage plus an output register. If BIO tries to read an empty FIFO, or write a full one, the BIO core stalls until the operation can complete.

There are arbitration rules when several producers compete:
- The host has priority.
- Lower-numbered BIO cores have priority over higher-numbered cores.
- The system does not promise fairness between competing writers.

A program should deliberately design its producer/consumer arrangement. A successful FIFO transfer proves that data moved; it does not prove that an external waveform or protocol is electrically correct.

## Events

Events let a BIO program wait for something other than a timer tick. For example, an event can be tied to FIFO fullness or an external event source.

BIO can wait on x30 until one of the selected events occurs. This is useful for coordinating multiple cores or waking a worker only when data is ready.

## BIO and DMA

BIO also has a DMA extension (BDMA). It allows BIO cores to read and write through the SoC bus. This can move data between memory and other destinations while the main CPU performs other work.

There is an important security rule: the BIO memory-access whitelist starts empty. Before BIO accesses main memory through BDMA, the host must explicitly allow the relevant memory region. The whitelist prevents BIO from becoming a way to bypass host-CPU memory protections.

## How BIO relates to the pins

BIO has 32 I/O bits:
- BIO[0:15] maps to PB[0:15].
- BIO[16:31] maps to PC[0:15].

The IO configuration block must route the chosen physical pad to BIO. When BIO is selected for a pin, it overrides the normal GPIO/alternate-function output control.

For example, the source mapping is:
- PC8 → BIO24
- PC10 → BIO26
- PC11 → BIO27

These are chip port names and BIO bit positions, not necessarily Dabao header numbers. Check the board pinout before wiring.

## What is a good BIO project?

BIO is suited to:
- LED string protocols.
- Timing-sensitive bit-banged buses.
- Custom serial signalling.
- Coordinated input sampling.
- Special-purpose DMA transformations.

The official book includes an SPI bit-bang example. It uses two BIO cores: one handles transmit and clock, while another handles receive. The code uses quantum synchronisation to keep the edges aligned. The book also recommends unrolling bit loops when predictable timing is important.

## Software development pointers

The official source recommends starting from the Xous-native examples in `libs/bio-lib`. A BIO program is wrapped with the `bio_code!` macro so the code loader can locate the compiled machine-code block.

For Xous, use the BIO resource manager to claim cores, FIFOs and pins rather than directly competing for shared resources.

## Where to read more

- [Official BIO chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/ch02-00-bio-overview.md)
- [BIO_BDMA register group](https://ci.betrusted.io/bao1x/bio_bdma.html)
- [BIO FIFO0](https://ci.betrusted.io/bao1x/bio_fifo0.html) · [FIFO1](https://ci.betrusted.io/bao1x/bio_fifo1.html) · [FIFO2](https://ci.betrusted.io/bao1x/bio_fifo2.html) · [FIFO3](https://ci.betrusted.io/bao1x/bio_fifo3.html)
- [Xous BIO examples](https://github.com/betrusted-io/xous-core/tree/dev/libs/bio-lib)
- [BIO RTL](https://github.com/baochip/baochip-1x/tree/main/rtl/modules/bio_bdma/rtl)

