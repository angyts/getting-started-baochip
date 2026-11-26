# Safety sensors and active protection

**In one sentence:** these blocks monitor the chip's physical environment and internal conditions for signs of abnormal voltage, light exposure, or tampering.

This is a hardware overview, not a security certification. The presence of a sensor block does not by itself prove that a production security policy has enabled it or responds correctly.

## MESH active protection

The book describes a metal protective grid near the top layer of the chip. The MESH logic monitors the integrity of this grid, intended to make invasive attacks such as die decapsulation or physical modification harder.

The documentation lists 64 mesh lines and active points along each line. It says detection can trigger interrupt alerts or system resets. It also describes hardware checking on wake from deep sleep in hardware-detection mode.

## Safety Environment Control (SEC)

The SEC-related sensors monitor several environmental and tamper conditions, including:
- External crystal oscillator frequency.
- High or low voltage.
- Optical/light sensing.
- Temperature.
- Voltage-regulator integrity.
- Power-on reset validation.
- Glue-logic validation.

The documentation describes configurable alarm, interrupt and reset behaviour.

## Voltage detectors

The book lists six voltage detectors:
- VDD09L and VDD09H: low/high monitoring associated with the internal VDD85 supply.
- VDD25L and VDD25H: low/high monitoring associated with VDD25.
- VDD33L and VDD33H: low/high monitoring associated with the external VDD33 supply.

The documented nominal trip points are 0.66 V, 1.08 V, 2.2 V, 2.82 V, 2.5 V and 4 V respectively. Each detector has configuration, filtering, enable and status functions.

## Light detector

The book describes two visible-light sensors for redundancy, placed at different chip corners. They can be enabled/disabled independently, filtered, self-tested, and queried for status.

## Glue chain

The documented glue-chain logic has 64 chains, with independent enable, status, reset and test-value controls.

## What this does not tell us

These descriptions explain what the hardware blocks are designed to detect. They do not prove which protection modes are enabled on a specific board, what policy Xous or Boot1 applies, or what irreversible action occurs in every configuration. Those details require the relevant boot/security documentation, source and silicon revision.

## References

- [Official safety chapter](https://github.com/baochip/baochip-1x/blob/main/docs/src/safety.md)
- [MESH register group](https://ci.betrusted.io/bao1x/mesh.html)
- [Sensor controller register group](https://ci.betrusted.io/bao1x/sensorc.html)
- [Glue-chain register group](https://ci.betrusted.io/bao1x/gluechain.html)

