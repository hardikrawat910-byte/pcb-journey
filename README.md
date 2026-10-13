# PCB Journey: from first board to mastery

![Boards](https://img.shields.io/badge/boards-1-ff69b4) ![KiCad](https://img.shields.io/badge/KiCad-9.0-314cb0) ![Started](https://img.shields.io/badge/started-Oct%202026-2ea043)

I'm **Hardik Rawat**, and this repo is my log of learning PCB design in KiCad. I'm aiming to design a new board every few days. Each board practises one new skill, and each one comes with the KiCad files, a review of what went wrong, and what I learned.

## Boards

| # | Board | Dates | New skill practised | Status |
| :-: | --- | --- | --- | --- |
| 01 | [Pulse Monitor](boards/01-pulse-monitor/) | 12–13 Oct 2026 | First full schematic → layout flow: custom symbols and footprints, a GND pour, a 2-layer stackup | ✅ DRC clean, not yet fabricated |

| 01 · Pulse Monitor |
| :---: |
| [![Board 01](boards/01-pulse-monitor/docs/images/render_3d.png)](boards/01-pulse-monitor/) |

## Skills roadmap

The roadmap below is a rough order. Each new board should tick off at least one item.

- [x] Schematic capture, custom symbols, custom footprints
- [x] Copper pours, a 2-layer stackup, passing DRC
- [ ] Ordering a board and assembling it (Gerbers, drill files, BOM)
- [ ] SMD parts (0805 to 0603) and hand soldering
- [ ] On-board power: an LDO, then a buck converter, laid out to its datasheet
- [ ] A bare microcontroller instead of a dev module (ESP32 or ATmega with its support parts)
- [ ] USB-C power and data, ESD protection
- [ ] Li-ion charging and battery protection
- [ ] Differential pairs and impedance-controlled traces
- [ ] A 4-layer board with solid ground and power planes
- [ ] Mixed-signal layout (analog sensor plus digital)

## Repo layout

```
.
├── README.md                     this journey log
├── boards/
│   └── 01-pulse-monitor/         one folder per board
│       ├── README.md             what it is, images, what I learned
│       ├── hardware/             KiCad project and local libraries
│       ├── bom/                  bill of materials
│       └── docs/                 design review and images
└── templates/
    └── BOARD_README_TEMPLATE.md  copy this when starting a new board
```

## Adding a new board

1. Create `boards/NN-short-name/` and put the KiCad project in `hardware/`. Keep custom libraries inside the project, linked with `${KIPRJMOD}`.
2. Copy `templates/BOARD_README_TEMPLATE.md` to the board folder as `README.md` and fill it in.
3. Add screenshots of the schematic, layout, 3D view and DRC result to `docs/images/`.
4. Add a row to the **Boards** table above and update the board-count badge.

## Tools

KiCad 9.0. Each board opens on its own. Clone the repo and open the `.kicad_pro` file inside the board's `hardware/` folder.
