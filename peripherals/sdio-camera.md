# SDIO and camera interfaces

This file groups two peripheral areas that appear in the Baochip register-reference index but are not yet explained in depth by the hand-written introductory book.

## SDIO

SDIO is a synchronous data interface commonly used to connect storage devices such as SD cards. The Baochip register index includes a [UDMA_SDIO block](https://ci.betrusted.io/bao1x/udma_sdio.html).

The index alone does not explain the exact supported card modes, bus widths, pin mapping, clock limits, DMA sequence or Xous driver status. Use the generated register description, the board pinout and the relevant Xous HAL code before planning hardware.

## Camera

The register index includes a [UDMA_CAMERA block](https://ci.betrusted.io/bao1x/udma_camera.html). The chip pinout also lists camera signals on some PB pins (for example, PB0–PB10 are shown with camera data/control alternate functions).

A camera interface normally needs more than just a data pin: confirm the exact clock, sync, data width, power/reset and sensor-side configuration. The Baochip book's introduction lists camera as a future topic for supplemental documentation, so this file does not claim a complete bring-up guide.

## References

- [UDMA_SDIO registers](https://ci.betrusted.io/bao1x/udma_sdio.html)
- [UDMA_CAMERA registers](https://ci.betrusted.io/bao1x/udma_camera.html)
- [Pinout and electrical specifications](https://github.com/baochip/baochip-1x/blob/main/docs/src/ch01-00-electrical-specifications.md)

