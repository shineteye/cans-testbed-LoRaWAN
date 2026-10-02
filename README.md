# LoRaWAN for a Smart Campus

An MPhil research project building a LoRaWAN testbed to measure and model indoor
radio propagation in the CANS building at the University of Cape Coast.

**Status: work in progress.** The building model and the measurement plan are
done. No field measurements have been collected yet. Every propagation number in
this repository is a prediction from assumed coefficients, not a result.

---

## What this project is

Large concrete teaching buildings are hard to cover with a wireless sensor
network. Signals lose strength when they pass through floors and walls, and the
loss depends on the path, not just the distance. This project builds a working
LoRaWAN testbed inside one such building, measures how the signal actually
behaves, and uses that data to fit a propagation model that can predict coverage
before anyone installs hardware.

The study area is the CANS building: four floors, thick concrete, six zones and
five courtyards running east to west.

Three questions drive the work:

1. How do floor level and structural depth affect received signal strength and
   signal to noise ratio inside the building?
2. How well do standard indoor propagation models, COST 231 multi-wall and
   ITU-R P.1238, describe what we measure?
3. Where should a gateway go to cover the building with the fewest gaps?

The theoretical framework is radio wave propagation theory. The research
methodology is Design Science Research.

---

## The building

Measured on site and converted from the original inch figures.

| Dimension | Value |
| --- | --- |
| East to west span | 175 m |
| North to south span | 51.56 m |
| Floors | 4 |
| Zones | 6 (Z1 at the east end, Z6 at the west) |
| Courtyards | 5 (S1 to S5, east to west) |

The north to south section is made of three bands:

| Band | Inches | Metres |
| --- | --- | --- |
| Laboratories, north | 450 | 11.43 |
| Walkway | 250 | 6.35 |
| Zone block, to the south | 1330 | 33.78 |
| **Total** | **2030** | **51.56** |

### How the parts fit together

Running east to west the plan alternates zone block and courtyard:
`Z1 S1 Z2 S2 Z3 S3 Z4 S4 Z5 S5 Z6`.

The courtyards are not all the same. S1, S3 and S5 are wide and open to the sky,
with grass at ground level and an external stair tower on the south strip. S2 and
S4 are narrow light wells covered by a translucent canopy, and they carry no
stair tower.

The laboratories run the full length of the building along the north side. The
walkway sits between the laboratories and the zone blocks and joins the two
structurally. It is roofed over, so it is an internal corridor rather than an
open slot. It runs the whole span without breaks, though the end zone continues
past the point where the walkway stops.

Two bridges cross out of the building to the north, lined up with S2 and S4, on
the second and third floors only. From the back these make the laboratory strip
look like three separate sections even though it is continuous.

Zones 1 and 4 each have an open fronted stair shaft at the east end of their
block, hard against the walkway and flush with the facade. These run the full
height of the building and no other zone has one.

### Corrections to earlier notes

Several things in earlier project notes are wrong and are superseded by the
measurements and photographs in this repository:

- The building is 175 m by 51.56 m, not 170 m by 80 m.
- S2 and S4 are covered light wells, not open courtyards.
- The laboratory strip is joined to the rest of the building by the walkway along
  its whole length. It is not detached with a single bridge at S3.
- The internal stair cores sit in Zones 1 and 4, at the east end of each block
  against the walkway. There are two, not three, and they are not in the south
  strip.

---

## Repository layout

```
.
├── README.md
├── model/                  3D geometry and gateway placement tool
│   └── cans_model_v11.html
├── docs/                   thesis chapters, proposal, progress reports
├── survey/                 site photographs and field notes
│   └── labelling.pdf       annotated shots used to build the model
├── data/                   measurement data (empty until the campaign runs)
│   ├── raw/
│   └── processed/
├── firmware/               end node sketches
├── gateway/                gateway configuration and setup notes
└── analysis/               scripts for fitting and plotting
```

---

## The building model

`model/cans_model_v11.html` is a parametric 3D and 2D model of the building,
built with Three.js. It is one self contained file. Open it in any modern browser
by double clicking it. There is nothing to install and no build step.

### What it does

- Draws the building from editable dimensions. Every measurement on the left
  panel rebuilds the geometry live.
- Switches between a 3D view and a top down plan view.
- Places a gateway on the roof or inside one of the open courtyards and draws the
  path from it to every zone on every floor.
- Estimates path loss for each of the 24 zone by floor cells and shows which
  route wins.
- Exports a PNG of either view, for use as a figure in the thesis.
- Exports and imports all settings as text, since a browser does not save what
  you typed into the page.

### Controls

| Action | How |
| --- | --- |
| Orbit | Drag |
| Pan | Shift drag, right drag, or middle drag |
| Pan in plan view | Any drag |
| Zoom | Scroll |
| Recentre | Fit button |

---

## The propagation estimate

This is the part to read carefully before trusting any number the model prints.

The model compares four ways a signal can get from the gateway to a node and
reports whichever is cheapest:

| Route | Path |
| --- | --- |
| Structure | Straight through, paying for every floor slab and wall between |
| Courtyard | Down an open courtyard beside the zone, then in through its face |
| Walkway | Down a courtyard, into the walkway, along it, then in |
| Shaft | Down the Zone 1 or Zone 4 stair shaft from the roof |

Loss is estimated with a multi-wall form at 868 MHz: free space loss, plus a
fixed cost per concrete wall, per floor slab, and per opening the signal enters
through.

### What the model currently predicts

With a roof mounted gateway, every one of the 24 cells is best served by dropping
into a courtyard next to the target zone. The walkway contributes nothing,
because with five courtyards among six zones no zone is ever more than one
courtyard away from an opening.

Move the gateway down into a courtyard and this inverts. The walkway then carries
16 of the 24 cells, because reaching a distant zone through the structure costs
more than travelling along the corridor.

The Zone 1 and Zone 4 stair shafts are close to break even. At the assumed
coefficient they lose to the courtyard route by less than 1 dB on the ground
floor and win on the second floor.

### Why none of this is a result

Every coefficient in the table below is an assumption. Three of them decide the
answers above, and the model is sensitive to all three.

| Coefficient | Assumed | Why it matters |
| --- | --- | --- |
| Loss per concrete wall | 12 dB | Scales every route |
| Loss per floor slab | 15 dB | Decides whether going through ever competes |
| Down an open courtyard | 5 dB | Decides the winning route for a roof mount |
| Along the walkway | 0.15 dB/m | Decides whether a low mount can reach far zones |
| Down a stair shaft | 7 dB | Swings Zones 1 and 4 either way within 2 dB |

Replacing these with measured values is the point of the measurement campaign.
Until that happens the tool ranks candidate gateway positions. It does not
predict coverage.

---

## Testbed

### Gateway

- Raspberry Pi running a fresh Raspberry Pi OS install
- RAK831 concentrator, 868 MHz, Semtech SX1301 baseband, connected over SPI
- ChirpStack installed natively through apt, no Docker
- EU868 channel plan
- Roof mounted, which is why floor level stays a variable in Research Question 1

Mounting needs permission from the UCC Network Section. Data collection is
waiting on that approval.

### End nodes

- Two LilyGO T-Beam boards, 868 MHz, version 1.1, configured through the Arduino
  IDE
- RAK811 boards held in reserve

---

## Measurement plan

Sampling is a two factor design: zone by floor, giving 24 cells.

| Item | Value |
| --- | --- |
| Measurement positions | 168 (24 anchor points plus 144 mobile) |
| Mobile points per cell | 6 |
| Uplinks per point per spreading factor | 100 |
| Spreading factor sweep | SF7 to SF12 at one anchor point per cell |
| Elsewhere | Single spreading factor, to stay inside duty cycle limits |

Four end nodes rotate through the six zones rather than one node sitting
permanently in each. Node identity is recorded at every point so that per device
differences can be separated from position effects.

---

## Open questions

Things that are still guesses and need a tape measure or another photograph.

- The depth of the south strip that closes the courtyards. Currently assumed at
  9 m. It sets how deep the courtyards cut into each block.
- Whether S4 has a stair attached to the Zone 4 face. One photograph suggests it
  does, which would contradict the rule that S2 and S4 carry no stairs.
- The construction of the internal partitions. Concrete, blockwork and drywall
  behave very differently at 868 MHz, and the loss per wall figure is meaningless
  until this is known.
- Which ground floor openings are perforated screen block rather than solid wall.
  These sit exactly where the hardest to reach nodes will be.

---

## Reproducing the model

No dependencies and no build. Open `model/cans_model_v11.html` in a browser.

The file loads Three.js r128 from a CDN, so it needs an internet connection the
first time. To work offline, download `three.min.js` next to the HTML file and
change the script tag to point at it.

To restore a particular configuration, paste an exported settings string into the
box at the bottom of the control panel and press Apply.

---

## Author

Shine Teye
University of Cape Coast
<https://www.shineteye.me/>

## Licence

Not yet chosen. Add a LICENSE file before making the repository public.
