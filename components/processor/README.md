# Dabao / Baochip-1x

[Official pinout](https://baochip.github.io/dabao/pinout/) · [Board repository](https://github.com/baochip/dabao) · [Xous](https://github.com/betrusted-io/xous-core).

Candidate interfaces, not final allocation: SPI2 PC0 clock (physical 14), PC1 data0 (12), PC2 data1 (11), PC3 CS0 (10). I2C0 PB11 SCL (27), PB12 SDA (26); these also have SPI alternate functions, so avoid pinmux conflicts. Pin 36 3V3, 38 GND, 40 VBUS, 39 VSYS. Verify physical orientation and exact board revision. Do not apply 5 V to GPIO or backfeed power rails. The board pinout does not advertise native I2S; BIO is a candidate, requiring timing and firmware validation. SD/ADC support is marked under development.

Before assembly: record board revision and regulator part/rating from schematic, measure idle rail and current, establish spare peak capacity, check GPIO electrical domains and reset states. Verify actual Xous driver APIs against pinned source; do not assume Arduino compatibility. Reserve distinct inputs for confirmation/cancel. On reset, no signing approval survives.
