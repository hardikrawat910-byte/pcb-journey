# Pulse Monitor PCB: my first PCB design

![First PCB](https://img.shields.io/badge/milestone-my%20first%20PCB-ff69b4) ![KiCad](https://img.shields.io/badge/KiCad-9.0-314cb0) ![Layers](https://img.shields.io/badge/board-82%20x%2049%20mm-2ea043) ![Status](https://img.shields.io/badge/status-routed%2C%20pre--order%20checks-yellow)

This is my first PCB, designed from scratch in KiCad 9. It is a carrier board that joins an **Arduino Nano**, a **MAX30102** heart-rate and SpO₂ sensor, a **0.96" SSD1306 OLED** and three navigation buttons, with an alarm LED. It is laid out for a pulse-monitor build.

Designed by **Hardik Rawat**.

![3D render](docs/images/render_3d.png)

> [!NOTE]
> **Status: routing complete, not yet fabricated.** Every net is connected and the GND pour is filled on both layers. A few checks remain before ordering, mainly setting the board to 2 copper layers. They are listed in [docs/design-review.md](docs/design-review.md), along with a pre-order checklist.

---

## What's on the board

| Part | Ref | Connects to |
| --- | --- | --- |
| Arduino Nano v3 | A1 | Main controller, powered over its USB port |
| MAX30102 sensor module | U1 | I²C: SDA → A4, SCL → A5; VCC → +5V |
| SSD1306 OLED module | U2 | I²C: SDA → A4, SCL → A5; VCC → +5V (shared bus) |
| MENU button | SW1 | D2, with R1 pull-up and C1 debounce cap |
| UP button | SW2 | D3, with R2 pull-up and C2 debounce cap |
| DOWN button | SW3 | D4, with R3 pull-up and C3 debounce cap |
| ALARM LED | D1 | D12 through R4 (1 kΩ) |

Both modules share one I²C bus. Their addresses don't clash: 0x57 for the MAX30102 and 0x3C for the SSD1306. The full parts list is in [bom/BOM.csv](bom/BOM.csv).

## Schematic

![Schematic](docs/images/schematic.png)

## PCB layout

82 × 49 mm, all through-hole parts, with tracks on the top (red) and bottom (blue) copper layers.

![PCB layout](docs/images/pcb_layout.png)

| 3D render with parts | Bare board |
| :---: | :---: |
| ![3D render](docs/images/render_3d.png) | ![Bare board](docs/images/render_3d_bare.png) |

## Repository layout

```
.
├── README.md
├── hardware/                     KiCad 9 project
│   ├── Praticenew.kicad_pro
│   ├── Praticenew.kicad_sch      schematic
│   ├── Praticenew.kicad_pcb      board layout
│   ├── sym-lib-table             project symbol library table
│   ├── fp-lib-table              project footprint library table
│   └── libraries/
│       ├── New_Library.kicad_sym           custom MAX30102 + SSD1306 symbols
│       └── Library.pretty/                 custom MAX30102 + SSD1306 footprints
├── bom/BOM.csv                   bill of materials
└── docs/
    ├── design-review.md          known issues and the pre-order checklist
    └── images/                   schematic, layout and 3D renders
```

## Opening the project

1. Install **KiCad 9.0 or newer**. The files are in KiCad 9 format and won't open in KiCad 8 or earlier.
2. Clone this repo and open `hardware/Praticenew.kicad_pro`.
3. The custom symbols and footprints load automatically through the project's `sym-lib-table` and `fp-lib-table`. You don't need to set up any global libraries.

## Manufacturing (once the checklist is done)

1. In the PCB editor, set the board to 2 layers, fill the zones (**B**) and run DRC until it shows 0 errors.
2. Go to File → Fabrication Outputs → Gerbers, and export F.Cu, B.Cu, F.Mask, B.Mask, F.Silkscreen, B.Silkscreen and Edge.Cuts.
3. Click **Generate Drill Files** (Excellon, PTH and NPTH merged).
4. Zip the outputs into `fabrication/` and check them in a Gerber viewer before uploading to a fab house.

Suggested order settings: 2 layers, 1.6 mm FR-4, HASL finish, 1 oz copper.

## Next steps

- [x] Finish routing and fill the GND pour
- [ ] Pass the pre-order checks ([design-review.md](docs/design-review.md))
- [ ] Export Gerbers and order the first batch
- [ ] Assemble, test, and add photos of the real board
- [ ] Write firmware: read the MAX30102, show BPM and SpO₂ on the OLED, and use the buttons for menus and alarm limits
