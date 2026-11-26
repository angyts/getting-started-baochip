# uDMA — moving data between peripherals and memory

**In one sentence:** uDMA is a hardware data-movement system that transfers data between memory and peripheral blocks without requiring the main CPU to copy every byte itself.

## The basic idea

Without DMA, software may need to:
1. Read a byte or word from a peripheral.
2. Store it into memory.
3. Repeat for every item.

With DMA, software configures a transfer and the hardware moves a block of data. The CPU can do other work while the transfer is in progress, then handle completion or errors.

The Baochip documentation index contains a uDMA controller plus several peripheral endpoints. The register reference lists:
- UART0–UART3.
- SPIM0–SPIM3 (SPI master).
- I2C0–I2C3.
- SDIO.
- I2S.
- Camera.
- Filter.
- SCIF.
- SPIS0–SPIS1 (SPI slave).
- ADC.

The hand-written Baochip book specifically lists UART, SPIM, I2C, SDIO and camera as topics for future supplemental driver documentation. The register groups exist, but the book does not provide a complete beginner bring-up recipe for every endpoint.

## What software still has to do

DMA does not magically know what the application wants. Software generally needs to:
- Configure the peripheral.
- Set up source/destination buffers and transfer sizes.
- Ensure the buffer is valid and accessible to the DMA engine.
- Start the transfer.
- Handle completion, error, and timeout cases.
- Keep buffers alive until the DMA no longer uses them.

The exact steps and register fields differ by DMA endpoint. Use that endpoint's generated register reference and the Xous driver source rather than copying one peripheral's sequence to another.

## uDMA and BIO BDMA are different blocks

The chip has more than one data-movement mechanism:
- **uDMA** is the dedicated peripheral DMA subsystem listed with UART, I²C, SPI, SDIO, camera and other endpoints.
- **BIO BDMA** is the bus-access/DMA extension attached to the programmable BIO coprocessor.

They have different programming models and should not be confused.

## Where to read more

- [uDMA controller](https://ci.betrusted.io/bao1x/udma_ctrl.html)
- [Xous HAL source](https://github.com/betrusted-io/xous-core/tree/main/libs/bao1x-hal/src/udma)
- [Official documentation introduction](https://github.com/baochip/baochip-1x/blob/main/docs/src/README.md)

