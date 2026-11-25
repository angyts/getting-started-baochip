# BaoBAB Mini V0.1 — working research notes

2026-11-25. Companion: [BaoBAB](https://github.com/angyts/BaoBAB). Design discussion snapshot, not an approved netlist. No additional purchases; use existing six 70 x 30 mm perfboards, headers, wires and modules. Keep two main boards, with small spare-board extensions if necessary. One five-way joystick and four dedicated buttons; no matrix (ghosting and security complexity). USB powered, removable Dabao, reopenable sandwich.

## Mechanical estimates

| Part | Nominal/estimated mm | Area mm² |
|---|---|---:|
| Waveshare HAT+ | 65 x 30.5 | 1982.5 |
| Dabao | ~51 x 21 | 1071 |
| MAX98357A | 19 x 18 | 342 |
| DRV2605L | ~20 x 18 | 360 |
| Speaker | ~15 x 15 | 225 |
| Motor | diameter 10 | 79 |
| Joystick | ~7.5 x 7.5 | 56 |
| Four buttons | 4 x 3 each | 48 |

Total ~4164 mm² versus 8400 mm² across four faces; area alone does not establish fit. Front parts ~2087 mm² versus 2100 mm², so extensions are necessary for practical clearance. Target ~85 x 31 x 35 mm; 85 mm is optimistic (65 mm display + 7.5 mm joystick + button cluster + gaps); allow longer if needed. Dimensions are not calibrated photo measurements. Screen overhang 0.25 mm each side if centered. Earlier four-board 70 x 60 mm concept rejected in favor of compact two-board design.

```text
FRONT
+------------------------------------------+
|   ^   +------------------------+    X    |
| < o > |      E-PAPER HAT+      |  Y   A  |
|   v   +------------------------+    B    |
+------------------------------------------+
REAR (orientation illustrative)
 USB <- [ DABAO on sockets ] [ SPEAKER ]
INSIDE
 [ MAX98357A ] [ DRV2605L ] [ MOTOR ]
           slack wiring harness
```

```text
 screen + control extensions
=========================== A
 amplifier / haptic / motor
       slack wiring
=========================== B
 female sockets / Dabao / speaker
```

Dry fit first. Solder with inner faces accessible, fold only unpowered, leave service loops and strain relief. Do not use wires as hinges or structural supports. Existing spare perfboard and header pins may form reinforcement/bridges on unused isolated pads, but pad strength is limited; no unsupported switches or exposed conductive fasteners near circuitry. Mechanical fastening and insulation remain unresolved. Speaker needs acoustic opening. Check HAT connector protrusion and opposing solder joints. No fixed 10 mm gap until measured.

## GPIO budget and candidate mapping

Official reference: https://baochip.github.io/dabao/pinout/ . Previous discussion counted 21 exposed GPIO, 11 peripheral signals and 9 inputs, leaving one nominal spare. Count and pinmux must be independently revalidated; do not treat this as a verified allocation. HAT enable, amplifier shutdown, RTC/EEPROM, debug and Xous reservations may alter budget.

| Function | Candidate physical pin | GPIO |
|---|---:|---|
| SPI CLK | 14 | PC0 |
| SPI DATA | 12 | PC1 |
| SPI CS | 10 | PC3 |
| Display DC | 11 | PC2 |
| Display RESET | 2 | PC12 |
| Display BUSY | 1 | PC13 |
| I2C SCL | 27 | PB11 |
| I2C SDA | 26 | PB12 |
| Audio BCLK | 4 | PC11 |
| Audio LRCLK | 5 | PC10 |
| Audio DATA | 6 | PC9 |
| Joystick UP | 7 | PC8 |
| Joystick DOWN | 9 | PC7 |
| Joystick LEFT | 35 | PB5 |
| Joystick RIGHT | 34 | PB4 |
| Joystick CENTER | 32 | PB3 |
| A confirm | 31 | PB2 |
| B cancel | 29 | PB1 |
| X | 15 | PB14 |
| Y | 16 | PB13 |

Candidate only: verify physical numbering, GPIO domains, SPI/I2C alternate functions, BIO mapping, Xous ownership and pull-ups. Audio BIO I2S unproven. One joystick + five buttons is theoretical maximum under this budget, not recommended. Two joysticks + four buttons would need 25 GPIO total; matrix rejected. Inputs active-low if common contact and pull-ups verified. Fresh press after display ready; stuck or ambiguous inputs fail closed.

## Display and power

Vendor reference: https://www.waveshare.com/wiki/2.13inch_e-Paper_HAT%2B . Prior research reported HAT+ PCB 65 x 30.5 mm, panel 59.2 x 29.2 x 1.05 mm, visible 48.55 x 23.71 mm, 250 x 122 pixels, SPI, 3.3 V operation, full refresh ~2 s, partial ~0.3 s, quick ~1.8 s, panel refresh power ~10.5 mW. These are previously reported vendor figures, not freshly verified in this commit; confirm revision, HAT supply versus panel supply, RTC, EEPROM, connector and total module current. E-paper retains stale images; never approve before fresh verified display and physical press. Slow refresh limits gaming.

Candidate rails: USB VBUS pin 40 for amplifier, 3V3 pin 36 for display/haptic/logic, common verified ground (candidate pin 38). NOT approved: check schematic, USB current allowance, regulator spare capacity, logic levels, pull-up rails, startup loads and backfeed. No simultaneous peak loads until tested. MAX98357A can exceed 0.5 W 8-ohm speaker rating (2 Vrms nominal); cap gain and volume, differential outputs never grounded. https://www.analog.com/media/en/technical-documentation/data-sheets/max98357a-max98357b.pdf . DRV2605L motor voltage/current unknown; verify supplier before powering. https://www.ti.com/lit/ds/symlink/drv2605l.pdf .

## Physical verification and assembly gates

- [ ] Dabao revision, header pitch/spacing, pinout and regulator schematic.
- [ ] HAT+ revision, header height, actual supply and controller.
- [ ] Joystick common and five contacts via unpowered continuity; buttons normally open.
- [ ] Motor rated voltage, startup/stall current; breakout pull-ups and straps.
- [ ] Speaker diameter/thickness, amplifier and haptic module height.
- [ ] Wire gauge, perfboard isolated pads, mechanical fastening and insulation.
- [ ] Dry fit two boards and control extensions; measure all opposing heights.
- [ ] Verify power budget and final GPIO netlist before soldering peripherals.
- [ ] Mount removable Dabao, test board alone; display; joystick; four buttons; haptic; audio; combined load.
- [ ] Fold and secure only after unpowered continuity/short checks and individual functional tests.

Wire convention: red labeled power, black ground, yellow clocks, blue data, green controls. Record actual measurements and firmware test commits in subsequent notes. No mains testing, real wallet keys or real funds. Existing component category notes remain the reference library; this file captures evolving project-specific decisions.
