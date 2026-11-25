# Digital audio

Purchased MAX98357A breakout (photo back marking VIN 2.5–5.5 V, default 9 dB, mono mix) and 0.5 W 8 ohm miniature speaker. [Analog Devices datasheet, rev 16](https://www.analog.com/media/en/technical-documentation/data-sheets/max98357a-max98357b.pdf).

A variant uses I2S, B variant left-justified. A accepts 16/24/32-bit I2S, 8–96 kHz sample rates; no MCLK or I2C configuration. Signals BCLK, LRCLK/WS, DIN, VIN, GND, GAIN, SD_MODE. Confirm breakout straps, silk and shutdown behavior before wiring. Gain choices 3/6/9/12/15 dB. Both speaker outputs are actively driven: connect speaker only across OUT+ and OUT-, neither to ground, never attach grounded scope clip to either. Keep pair short/twisted. 

Speaker power calculation: sqrt(0.5 W x 8 ohm)=2 Vrms, 0.25 Arms nominal; use margin (e.g. target 0.25 W = 1.41 Vrms). The amplifier can overpower speaker even with 3.3 V supply. Begin with very low digital volume and lowest verified hardware gain; characterize with suitable differential audio measurement/dummy load, not raw class-D PWM on ordinary AC meter. No full-scale test on small speaker. Check peak supply draw and local bypassing; dedicated rail only after power review.

BIO candidate: 16 kHz signed PCM, stereo 16-bit slots gives 512 kHz BCLK, LRCLK 16 kHz; standard I2S MSB one bit after WS transition. Duplicate mono to both slots. Verify waveforms with logic analyzer before speaker, then low-volume tone, stream bounded clips, silence on underrun/reset. Audio from host is advisory, never trusted transaction data. Cap duration and amplitude; no audio callback may trigger signing.
