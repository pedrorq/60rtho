# 60rtho firmware notes

Everything firmware needs to know about the 60rtho PCB, read straight from
`60rtho.kicad_pcb` and `60rtho.kicad_sch` (state of commit `517d2d5`, 2026-11-17).
If the board changes, re-check the pin map against the KiCad files.

## Status of the hardware

- PCB routing is not finished yet. Markus is routing the MCU (RP2040) area and the
  USB-C connector. No board has been manufactured, so firmware can be written and
  compiled now, but not tested on real hardware.
- Pin assignments below come from the schematic and the placed footprints, so they
  should not change unless Markus swaps pins while routing the MCU area.

## Main parts

| Ref | Part | Notes |
|---|---|---|
| U62 | RP2040 | QFN-56 |
| U64 | W25Q128JVS | 16 MB QSPI flash (W25Q-compatible boot2 works) |
| Y1 | 12 MHz crystal | standard RP2040 clock |
| U65 | AMS1117-3.3 | 5 V USB to 3.3 V regulator (feeds the MCU AND the underglow LEDs) |
| U63 | USBLC6-2SC6 | USB ESD protection |
| J1 | USB-C receptacle (USB 2.0) | 5.1k CC resistors R1/R2, 22 ohm series resistors R3/R4 on D+/D- |
| SW1 | Reset button | pulls RUN (pin 26) to GND |
| SW2 | Boot button/switch | pulls QSPI_SS to GND through R7 (1k): enters the USB bootloader |
| D62 | Red status LED | on GPIO20 through R8 (1k), active high |
| J2 | 3-pin SWD header | pin 1 SWDIO, pin 2 GND, pin 3 SWCLK (optional debugging) |
| LED1 to LED12 | SK6812MINI | underglow, powered from 3.3 V |
| U1 to U75 (71 used) | Cherry MX hot-swap sockets | one diode each, a few alternates share a diode |

## RP2040 pin map

| Function | GPIO | RP2040 pin |
|---|---|---|
| COL1 | GP0 | 2 |
| COL2 | GP1 | 3 |
| COL3 | GP2 | 4 |
| COL4 | GP3 | 5 |
| COL5 | GP4 | 6 |
| COL6 | GP5 | 7 |
| COL7 | GP6 | 8 |
| COL8 | GP7 | 9 |
| COL9 | GP8 | 11 |
| COL10 | GP9 | 12 |
| COL11 | GP10 | 13 |
| COL12 | GP11 | 14 |
| COL13 | GP12 | 15 |
| COL14 | GP13 | 16 |
| ROW1 | GP14 | 17 |
| ROW2 | GP15 | 18 |
| ROW3 | GP16 | 27 |
| ROW4 | GP17 | 28 |
| ROW5 | GP18 | 29 |
| Underglow data (RGBData1) | GP19 | 30 |
| Status LED | GP20 | 31 |
| Unused | GP21 to GP29 | |

USB D-/D+ are the RP2040's native USB pins (46/47). SWCLK pin 24, SWDIO pin 25.

## Matrix

- 5 rows x 14 columns.
- Diodes: cathode (K) on the ROW, anode (A) on the switch, switch other side on the
  COLUMN. So current flows column to row: **`DIODE_DIRECTION COL2ROW`**.
- QMK style pin lists:
  - `cols`: GP0, GP1, GP2, GP3, GP4, GP5, GP6, GP7, GP8, GP9, GP10, GP11, GP12, GP13
  - `rows`: GP14, GP15, GP16, GP17, GP18

### Matrix grid (row, column) with the likely key in each spot

Rows and columns are numbered from 1 here; QMK counts from 0, so subtract 1.
Key names are a best guess from switch positions; confirm with Pedro before finalising
the keymap. "or" means two alternative switch footprints that share one diode, so
only one of them is fitted and both give the same matrix position.

| Row | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | C9 | C10 | C11 | C12 | C13 | C14 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Esc | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | - | = | Backspace (1u U66 or 2u U14) |
| 2 | Tab | Q | W | E | R | T | Y | U | I | O | P | [ | ] (U27 or U67) | \ |
| 3 | Caps | A | S | D | F | G | H | J | K | L | ; | ' | (none) | Enter |
| 4 | LShift | Z | X | C | V | B | N | M | , | . | / | (none) | Up | RShift (2.25u U69 or 2.75u U53) |
| 5 | LCtrl | LGui | LAlt | Split space left 2.25u | (none) | Space 6.25u (U57) or split middle 1.25u (U71) | Split space right 2.75u | (none) | (none) | RAlt (U58) or Left (U73) | RGui/Fn (U59) or Down (U74) | Menu (U60) or Right (U75) | (none) | RCtrl |

### Every switch

`x` is the switch centre in key units (1u = 19.05 mm) measured from the left edge of
the bottom-left key (LCtrl). Rows are 1u apart, top row = 0. The alpha columns are
vertically aligned (that is the "rtho" part: Q, A and Z sit straight under 1, and so on).

| Switch | Row | Col | Diode | x (centre, u) | Likely key / width | Shares diode with |
|---|---|---|---|---|---|---|
| U1 | 1 | 1 | D1 | 1.25 | Esc 1u | |
| U2 | 1 | 2 | D2 | 2.25 | 1 | |
| U3 | 1 | 3 | D3 | 3.25 | 2 | |
| U4 | 1 | 4 | D4 | 4.25 | 3 | |
| U5 | 1 | 5 | D5 | 5.25 | 4 | |
| U6 | 1 | 6 | D6 | 6.25 | 5 | |
| U7 | 1 | 7 | D7 | 7.25 | 6 | |
| U8 | 1 | 8 | D8 | 8.25 | 7 | |
| U9 | 1 | 9 | D9 | 9.25 | 8 | |
| U10 | 1 | 10 | D10 | 10.25 | 9 | |
| U11 | 1 | 11 | D11 | 11.25 | 0 | |
| U12 | 1 | 12 | D12 | 12.25 | - | |
| U13 | 1 | 13 | D13 | 13.25 | = | |
| U14 | 1 | 14 | D14 | 13.75 | Backspace 2u (stabilizer ST1); its keycap covers the U13 + U66 spots | U66 |
| U66 | 1 | 14 | D14 | 14.25 | Backspace 1u | U14 |
| U15 | 2 | 1 | D15 | 1.00 | Tab 1.5u | |
| U16 | 2 | 2 | D16 | 2.25 | Q | |
| U17 | 2 | 3 | D17 | 3.25 | W | |
| U18 | 2 | 4 | D18 | 4.25 | E | |
| U19 | 2 | 5 | D19 | 5.25 | R | |
| U20 | 2 | 6 | D20 | 6.25 | T | |
| U21 | 2 | 7 | D21 | 7.25 | Y | |
| U22 | 2 | 8 | D22 | 8.25 | U | |
| U23 | 2 | 9 | D23 | 9.25 | I | |
| U24 | 2 | 10 | D24 | 10.25 | O | |
| U25 | 2 | 11 | D25 | 11.25 | P | |
| U26 | 2 | 12 | D26 | 12.25 | [ | |
| U27 | 2 | 13 | D27 | 13.25 | ] 1u | U67 |
| U67 | 2 | 13 | D27 | 13.50 | alternate position for the same key (wider cap) | U27 |
| U28 | 2 | 14 | D28 | 14.25 | \ 1u | |
| U29 | 3 | 1 | D29 | 0.875 | Caps Lock 1.75u | |
| U30 | 3 | 2 | D30 | 2.25 | A | |
| U31 | 3 | 3 | D31 | 3.25 | S | |
| U32 | 3 | 4 | D32 | 4.25 | D | |
| U33 | 3 | 5 | D33 | 5.25 | F | |
| U34 | 3 | 6 | D34 | 6.25 | G | |
| U35 | 3 | 7 | D35 | 7.25 | H | |
| U36 | 3 | 8 | D36 | 8.25 | J | |
| U37 | 3 | 9 | D37 | 9.25 | K | |
| U38 | 3 | 10 | D38 | 10.25 | L | |
| U39 | 3 | 11 | D39 | 11.25 | ; | |
| U40 | 3 | 12 | D40 | 12.25 | ' | |
| U41 | 3 | 14 | D41 | 13.875 | Enter 2.25u (stabilizer ST2) | |
| U42 | 4 | 1 | D42 | 0.875 | Left Shift 1.75u | |
| U43 | 4 | 2 | D43 | 2.25 | Z | |
| U44 | 4 | 3 | D44 | 3.25 | X | |
| U45 | 4 | 4 | D45 | 4.25 | C | |
| U46 | 4 | 5 | D46 | 5.25 | V | |
| U47 | 4 | 6 | D47 | 6.25 | B | |
| U48 | 4 | 7 | D48 | 7.25 | N | |
| U49 | 4 | 8 | D49 | 8.25 | M | |
| U50 | 4 | 9 | D50 | 9.25 | , | |
| U51 | 4 | 10 | D51 | 10.25 | . | |
| U52 | 4 | 11 | D52 | 11.25 | / | |
| U68 | 4 | 13 | D66 | 12.25 | Up arrow 1u (sits right above Down) | |
| U69 | 4 | 14 | D53 | 13.875 | Right Shift 2.25u (stabilizer ST3) | U53 |
| U53 | 4 | 14 | D53 | 13.125 | Right Shift 2.75u (covers the Up spot, so no Up arrow) | U69 |
| U54 | 5 | 1 | D54 | 0.625 | Left Ctrl 1.25u | |
| U55 | 5 | 2 | D55 | 1.875 | Left Gui/Win 1.25u | |
| U56 | 5 | 3 | D56 | 3.125 | Left Alt 1.25u | |
| U70 | 5 | 4 | D68 | 4.875 | Split space, left part 2.25u (stabilizer ST5) | |
| U57 | 5 | 6 | D57 | 6.875 | Space 6.25u (stabilizer ST6) | U71 |
| U71 | 5 | 6 | D57 | 6.625 | Split space, middle part 1.25u | U57 |
| U72 | 5 | 7 | D70 | 8.625 | Split space, right part 2.75u (stabilizer ST4) | |
| U58 | 5 | 10 | D58 | 10.625 | Right Alt 1.25u | U73 |
| U73 | 5 | 10 | D58 | 11.25 | Left arrow 1u | U58 |
| U59 | 5 | 11 | D59 | 11.875 | Right Gui or Fn 1.25u | U74 |
| U74 | 5 | 11 | D59 | 12.25 | Down arrow 1u | U59 |
| U60 | 5 | 12 | D60 | 13.125 | Menu 1.25u | U75 |
| U75 | 5 | 12 | D60 | 13.25 | Right arrow 1u | U60 |
| U61 | 5 | 14 | D61 | 14.375 | Right Ctrl 1.25u | |

Layout options this gives (good candidates for QMK `layout_options` / Vial layout options):

1. Backspace: 1u (U66, keeps "=" U13) or 2u (U14).
2. Row 2 key at column 13: U27 or U67.
3. Right Shift: 2.75u (U53) or Up arrow + 2.25u Right Shift (U68 + U69).
4. Space bar: 6.25u (U57) or split 2.25u + 1.25u + 2.75u (U70 + U71 + U72).
5. Bottom right: four 1.25u mods (U58, U59, U60, U61) or Left/Down/Right arrows (U73, U74, U75) + Right Ctrl.

The arrow cluster (Up U68 above Down U74) only makes sense with options 3b and 5b together.

## Underglow

- 12 x SK6812MINI (WS2812-compatible, GRB), data on **GP19**, one single chain.
- Powered from the 3.3 V regulator (AMS1117-3.3), not from 5 V. Data logic is 3.3 V, so
  no level shifting issue.
- **Keep brightness limited** (for example QMK `RGBLIGHT_LIMIT_VAL` around 120 out of 255).
  All 12 LEDs at full white can draw several hundred mA, which the AMS1117 has to drop
  from 5 V to 3.3 V as heat, while also powering the RP2040.
- Chain order (data enters LED1, LED12's output is unused). Viewed from the top of the
  keyboard, the chain runs clockwise starting bottom middle:

| LED | Position (PCB mm, x / y) | Where |
|---|---|---|
| LED1 | 137.2 / 156.1 | bottom edge, left of centre (below the MCU) |
| LED2 | 85.8 / 155.4 | bottom left |
| LED3 | 65.8 / 111.5 | left edge, middle |
| LED4 | 90.8 / 68.2 | top left |
| LED5 | 147.8 / 68.2 | top |
| LED6 | 204.8 / 68.2 | top, centre |
| LED7 | 261.8 / 68.2 | top |
| LED8 | 305.7 / 67.8 | top right |
| LED9 | 343.8 / 111.5 | right edge, middle |
| LED10 | 323.9 / 155.8 | bottom right |
| LED11 | 252.6 / 156.3 | bottom, right of centre |
| LED12 | 208.5 / 155.5 | bottom, centre |

For reference, switch U1 (Esc) is at 85.73 / 73.82 and U61 (Right Ctrl) at 335.76 / 150.02 in
the same coordinates (y grows downwards). The LEDs are on the bottom side of the PCB (underglow
only, no per-key LEDs), so QMK `rgblight` (underglow) fits better than `rgb_matrix`.

## Status LED

- D62, red, on **GP20**, active high (GPIO high = LED on). Could be used for Caps Lock,
  a layer indicator or "bootloader / power" indication.

## Bootloader and flashing

- Enter the RP2040 USB bootloader: hold SW2 (BOOT), press and release SW1 (RESET), then
  release SW2. Or hold SW2 while plugging in USB. A drive called `RPI-RP2` appears; copy
  the `.uf2` file onto it.
- Worth enabling in firmware: a `QK_BOOT` key in the keymap, and/or QMK's
  `RP2040_BOOTLOADER_DOUBLE_TAP_RESET` so a double tap of RESET enters the bootloader.

## Suggested firmware direction (not decided yet)

- QMK (`platform: rp2040`, `bootloader: rp2040`, data-driven `keyboard.json`), optionally
  Vial on top so keys can be remapped without reflashing.
- WS2812 driver: `vendor` (RP2040 PIO driver).
- USB VID/PID: a placeholder (for example 0xFEED / 0x6001) is fine for personal use.
- Things to confirm with Pedro (and Markus) first: which layout options will actually be
  built, the exact key legends, keyboard name/manufacturer strings, and whether to use Vial.
