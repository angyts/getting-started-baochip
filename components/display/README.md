# E-paper display and adapter

Purchased: Waveshare 2.13inch e-Paper HAT+ with RTC; photograph confirms HAT+ marking and fitted Pi-style header. Separate generic 8-to-24 driver ordered, arrival unverified. Do not confuse HAT+, older HAT revisions, touch HAT or bare panel. 
Sources: [HAT+ schematic](https://files.waveshare.com/wiki/2.13inch-%20e-Paper-HAT%2B/2.13inch_e-Paper_HAT%2B-SCH.pdf), [vendor wiki](https://www.waveshare.com/wiki/2.13inch_e-Paper_HAT+), [vendor code](https://github.com/waveshareteam/e-Paper). Wiki fetch was blocked; exact controller, resolution, RTC type/address and header net mapping remain to be checked against full schematic and panel markings.

The HAT contains panel drive circuitry, RTC and EEPROM. Raw 24-pin FPC carries logic plus generated panel bias voltages; never connect directly to GPIO. Use assembled HAT first, not both drivers. Generic adapter needs exact FPC contact orientation, pitch, pinout and panel compatibility. Do not hot-plug FPC.

Logical interface: SPI clock/data/CS, D/C, reset, BUSY input, power and ground; RTC may need separate I2C. Raspberry Pi pin numbers are not Dabao pin numbers. Confirm HAT supply rail and logic voltage, power enable and level shifting. Do not guess 3.3 vs 5 V from connector shape. Firmware: controller-specific reset/init, framebuffer packing, refresh command, bounded BUSY wait, sleep and recovery; verify polarity and full/partial refresh limits. A 250x122 one-bit frame with each row padded to 32 bytes requires 32 x 122 = 3904 bytes; this is illustrative, not confirmed panel geometry. Actual stride/orientation depends on controller.

Test full white/black, checkerboard, text, rotation, partial update only if supported, recovery from timeout and unplug. E-paper retains old pixels after power loss: invalidate approval on boot/reset, display a fresh request identifier, await refresh completion, then require new physical press. Never sign on stale display or display timeout.
