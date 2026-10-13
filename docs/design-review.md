# Design review: what to fix before ordering

This review is based on reading `Praticenew.kicad_pcb` and `Praticenew.kicad_sch` directly.

**Current status: routing complete.** A connectivity check that includes the filled zones finds every net fully connected (GND 14 pads, +5V 6, SDA 3, SCL 3, D2/D3/D4 5 each, LED 2 + 2). The items below are what is left before ordering.

## Fixed

### 1. GND copper pour filled ✅
The GND zone was originally saved without any fill, which left every ground pin isolated. It is now filled on F.Cu and B.Cu, and all 14 GND pads join it.

### 2. Button pull-up resistors: these were never actually missing
An earlier version of this review said that R1, R2 and R3 were not routed. That was wrong: the review's checker only counted a track as connected when it *ended* on a pad. The tracks from C1/C2/C3 to the MENU/UP/DOWN buttons run straight through the centre of R1/R2/R3 pad 2, so the resistors are connected. The second leg of each switch is joined inside the switch itself.

## Should fix

### 3. The board is set to 4 copper layers but only uses 2
The stackup has F.Cu, In1.Cu, In2.Cu and B.Cu, but every track and both pours are on F.Cu or B.Cu. A 4-layer order costs several times more than a 2-layer one, and the inner layers would be empty.

**Fix:** go to Board Setup → Board Stackup → Physical Stackup and set Copper layers to 2.

### 4. Schematic and PCB reference designators don't match
The schematic names the parts SW1, SW2, SW3 and D1. The PCB has them renamed to MENU, UP, DOWN and ALARM. The next "Update PCB from Schematic" will flag or undo this.

**Fix:** put the references back to SW1–SW3 and D1, and add MENU, UP, DOWN and ALARM as separate silkscreen text (Place → Text). That way the labels survive updates.

### 5. The debounce RC is about 50 times too fast
1 kΩ × 100 nF = **0.1 ms**, while switch bounce typically lasts about 1–10 ms. A 1 kΩ pull-up also draws 5 mA for as long as a button is held.

**Fix:** change R1–R3 to **10 kΩ**. That gives 1 ms and 0.5 mA. Keep a short software debounce as well.

### 6. Check your MAX30102 module's I²C pull-up voltage
Many purple MAX30102 breakout boards pull SDA and SCL up to 1.8 V internally. The Arduino Nano is a 5 V part and may not read those levels reliably. Before you order, measure SDA to GND on your own module with it powered.

### 7. ERC will complain about GND not being driven
No `PWR_FLAG` is placed, so ERC reports "power pin not driven" on GND.

**Fix:** add a PWR_FLAG symbol on the GND net (and on +5V if ERC flags it too).

## Nice to have

- **Check the custom footprints against the real modules.** Measure the header pin order and spacing of your MAX30102 and SSD1306 boards against `Library:MAX30102_Module` and `Library:SSD1306` before ordering. A reversed header is the most common first-PCB mistake.
- **Fix the OLED footprint's value.** `SSD1306.kicad_mod` has its Value set to "Untitled", which shows on the Fab layer.
- **Add mounting holes.** Four M3 holes in the corners make the board easy to mount.

## Checklist before ordering

- [x] Zones filled (B) and the file saved
- [x] All nets connected, including R1–R3 to D2/D3/D4
- [ ] Stackup set to 2 layers
- [ ] References back in sync with the schematic
- [ ] ERC: 0 errors
- [ ] DRC: 0 errors, 0 unconnected items
- [ ] Footprint pin order checked against the real modules
- [ ] Gerbers and drill files exported to `fabrication/` and checked in a Gerber viewer
