# Design review: what to fix before ordering

This review is based on reading `Praticenew.kicad_pcb` and `Praticenew.kicad_sch` directly.

**Current status: routing complete.** A connectivity check that includes the filled zones finds every net fully connected (GND 14 pads, +5V 6, SDA 3, SCL 3, D2/D3/D4 5 each, LED 2 + 2). The items below are what is left before ordering.

## Fixed

### 1. GND copper pour filled ✅
The GND zone was originally saved without any fill, which left every ground pin isolated. It is now filled on F.Cu and B.Cu, and all 14 GND pads join it.

### 2. Button pull-up resistors: these were never actually missing
An earlier version of this review said that R1, R2 and R3 were not routed. That was wrong: the review's checker only counted a track as connected when it *ended* on a pad. The tracks from C1/C2/C3 to the MENU/UP/DOWN buttons run straight through the centre of R1/R2/R3 pad 2, so the resistors are connected. The second leg of each switch is joined inside the switch itself.

### 3. Board set to 2 copper layers ✅
The stackup used to have four copper layers (F.Cu, In1.Cu, In2.Cu, B.Cu) with the inner two empty, which would have been priced as a 4-layer board. It is now 2 layers, 1.6 mm FR-4.

### 4. Pull-ups changed to 10 kΩ ✅
With 1 kΩ × 100 nF the debounce time constant was only 0.1 ms, and a held button drew 5 mA. R1–R3 are now 10 kΩ in both the schematic and the PCB, which gives 1 ms and 0.5 mA. R4, the LED resistor, stays at 1 kΩ.

### 5. OLED footprint value fixed ✅
U2 now shows "OLED" instead of "Untitled" on the PCB. The schematic still calls it "SSD1306", which a schematic-parity check will flag. That is cosmetic.

### DRC result ✅
DRC (with zones refilled) reports **0 errors and 0 unconnected items**. See [the screenshot](images/drc_result.png).

It also reports one warning: *Footprint 'SSD1306' does not match copy in library 'Library'*. The footprint on the board has its four pads moved about 0.64 mm up and 0.07 mm left compared with `Library.pretty/SSD1306.kicad_mod`. The 2.54 mm pitch is the same in both. The board copy is the one the tracks are routed to, so **don't** use "Update Footprint from Library" for U2. Instead, save the board's copy back into the library, or exclude the warning.

## Should fix

### 6. Schematic and PCB reference designators don't match
The schematic names the parts SW1, SW2, SW3 and D1. The PCB has them renamed to MENU, UP, DOWN and ALARM. The next "Update PCB from Schematic" will flag or undo this.

**Fix:** put the references back to SW1–SW3 and D1, and add MENU, UP, DOWN and ALARM as separate silkscreen text (Place → Text). That way the labels survive updates.

### 7. Check your MAX30102 module's I²C pull-up voltage
Many purple MAX30102 breakout boards pull SDA and SCL up to 1.8 V internally. The Arduino Nano is a 5 V part and may not read those levels reliably. Before you order, measure SDA to GND on your own module with it powered.

### 8. ERC will complain about GND not being driven
No `PWR_FLAG` is placed, so ERC reports "power pin not driven" on GND.

**Fix:** add a PWR_FLAG symbol on the GND net (and on +5V if ERC flags it too).

## Nice to have

- **Check the custom footprints against the real modules.** Measure the header pin order and spacing of your MAX30102 and SSD1306 boards against `Library:MAX30102_Module` and `Library:SSD1306` before ordering. A reversed header is the most common first-PCB mistake.
- **Add mounting holes.** Four M3 holes in the corners make the board easy to mount.

## Checklist before ordering

- [x] Zones filled (B) and the file saved
- [x] All nets connected, including R1–R3 to D2/D3/D4
- [x] Stackup set to 2 layers
- [x] R1–R3 changed to 10 kΩ on the PCB
- [x] R1–R3 changed to 10 kΩ in the schematic too
- [ ] References back in sync with the schematic
- [ ] ERC: 0 errors
- [x] DRC: 0 errors, 0 unconnected items (1 footprint-library warning, see above)
- [ ] Footprint pin order checked against the real modules
- [ ] Gerbers and drill files exported to `fabrication/` and checked in a Gerber viewer
