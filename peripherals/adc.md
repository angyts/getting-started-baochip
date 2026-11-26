# ADC — converting voltage into a number

**In one sentence:** an ADC (analogue-to-digital converter) measures an analogue voltage and turns it into a digital number that software can read.

The Baochip ADC chapter describes a 10-bit successive-approximation ADC. It supports external voltage measurement and an on-chip temperature sensor.

## What does 10-bit mean?

A 10-bit result can represent 1,024 levels, from 0 through 1,023. The documentation gives this conversion formula:

`VIN = dout / 1023 × Vbg`

The typical bandgap reference is 1.208 V. The book's example at 25°C uses a reading of 516:

`VIN = (516 / 1023) × 1.208 ≈ 0.609 V`

This is the ADC input voltage, not necessarily the voltage of a higher-voltage signal before any external divider or conditioning circuit.

## External voltage inputs

The documentation maps four ADC inputs to Port A:
- PA04 = ADC0.
- PA05 = ADC1.
- PA06 = ADC2.
- PA07 = ADC3.

The listed VIN range is 0.3 V to 1.0 V. Do not connect a 3.3 V signal directly to an ADC input based on this document. A suitable external divider or conditioning circuit is needed, designed against the actual board and input limits.

## Temperature sensor

The ADC can also read a voltage that varies with the chip junction temperature. The documentation lists:
- Temperature range: −40°C to 125°C.
- Typical temperature resolution: 0.6°C.
- Accuracy: ±4°C.
- Temperature reference table from −40°C to 125°C.

The temperature sensor requires an enable sequence involving the bandgap circuit, voltage buffers and ADC. It is not simply a register read with no setup.

## Speed and clocks

The book lists:
- Up to 100 kS/s.
- At least 14 ADC clock cycles per conversion.
- ADC clock range 0.2 MHz to 1.6 MHz.
- Bandgap clock range 8 MHz to 16 MHz.
- One RX DMA channel.

The conversion starts when `adc_sample` goes high. The book says conversion takes 14 clock cycles and the data-valid signal/result appears at the 15th clock cycle.

## Software interpretation

The ADC gives a number proportional to the selected input voltage. Software still needs to know:
- Which input is selected.
- The active reference voltage.
- Any external divider/gain.
- The enable and conversion timing sequence.
- How the result is delivered (including the RX DMA path).

Do not equate “ADC returned a number” with a calibrated voltage measurement until the full signal path and reference are checked.

## References

- [Official ADC chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/ch04-00-adc.md)
- [UDMA_ADC register group](https://ci.betrusted.io/bao1x/udma_adc.html)

