# 60rtho

Custom 60%/65%-style keyboard PCB designed in KiCad 10 by Pedro and Markus:
RP2040, USB-C, 71 Cherry MX hot-swap switches, 12 SK6812MINI underglow LEDs.

## Read first

- **FIRMWARE.md** has every board fact firmware needs: pin map, matrix, diode
  direction, layout options, underglow, status LED, bootloader. Use it as the source
  of truth, and re-check against the KiCad files if something looks off.

## Rules for working in this repo

- Do NOT modify the KiCad files (`*.kicad_pcb`, `*.kicad_sch`, `*.kicad_pro`,
  `*.kicad_prl`, footprint and symbol libraries) unless Pedro explicitly asks.
  Markus is still routing the board and owns those changes.
- Keep firmware work in its own folder (for example `firmware/`).
- Pedro is new to electronics and firmware, and works on Windows. Explain steps in
  plain words and give exact commands he can copy.
- Writing style: never use em dashes in any output (code comments, docs, commit
  messages, chat).
- Commit and push only when Pedro asks.
