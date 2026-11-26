# PWM — generating timed pulses

**In one sentence:** PWM makes a digital output HIGH for part of each repeating period and LOW for the rest. By changing that proportion, software can control average power or represent a changing signal.

PWM is often used for LED brightness, motor control, simple beepers and control signals, provided the target device supports the chosen frequency and electrical drive.

## Frequency and duty cycle

- **Frequency** says how often the pattern repeats.
- **Period** is the time for one repeat: period = 1 / frequency.
- **Duty cycle** is the percentage of that period for which the output is HIGH.

Example: a 25% duty cycle means the signal is HIGH for one quarter of each period and LOW for the remaining three quarters.

## Baochip PWM hardware

The official book describes:
- 4 PWM timers.
- 4 independently configurable channels per timer.
- 16-bit resolution.
- Multiple output modes.
- GPIO-triggered modes for rising edge, falling edge or either edge.
- An event from each timer when its counter reaches its configured threshold.

The register block base address in the book is `0x5012_0000`. The generated register index also contains a [PWM register group](https://ci.betrusted.io/bao1x/pwm.html).

## How PWM frequency is made

The PWM clock path can select between PCLK and a divided clock source. A prescaler then divides the selected clock, and a threshold controls the counter period.

The book provides an example that derives a 5 Hz output using:
- A 48 MHz source divided by 2 × 1000 = 24 kHz.
- A prescaler of 48 = 500 Hz.
- A threshold of 100 = 5 Hz.

The example is useful for understanding the stages; do not copy its values without checking the selected clock source, pin mapping and actual timer configuration.

## What PWM is not

PWM is not the same as an analogue voltage output. The pin is still switching between digital levels. A load may respond to the average power, but the waveform itself is a pulse train.

PWM is also not an I²S audio interface. It can make simple tones or drive certain audio circuits, but it does not directly provide the serial PCM protocol used by an I²S amplifier.

## References

- [Official PWM chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/pwm.md)
- [PWM register group](https://ci.betrusted.io/bao1x/pwm.html)

