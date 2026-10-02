# LoRaWAN for a Smart Campus

A LoRaWAN testbed built inside the CANS building at the University of Cape Coast. The goal is to measure how LoRa signals behave indoors in a large concrete building and use those measurements to build a propagation model for campus IoT planning.

## What we are building

A single gateway on the CANS rooftop, a set of mobile end nodes carried through the building, and a network server that logs every uplink. Each uplink is tagged with where it was sent from, which floor it was on, and which spreading factor was used. That log becomes the dataset for the propagation model.

## The building

CANS is 170 m by 80 m, four floors, thick concrete. The plan is a comb shape: one long south spine of offices running east to west, with six zone blocks projecting north off it and five open courtyards in between.

Running east to west: Z1, S1, Z2, S2, Z3, S3, Z4, S4, Z5, S5, Z6. Z1 is at the east end.

Three stair cores sit inside the south spine at S1, S3 and S5. The north wing is detached and joined only by a bridge and stair at S3.

## Hardware

| Part | What it is |
|------|------------|
| Gateway host | Raspberry Pi, fresh Raspberry Pi OS install |
| Concentrator | RAK831, 868 MHz, Semtech SX1301, bare module with a 24-pin header, no GPS |
| Connection | SPI between the RAK831 and the Pi |
| End nodes | 2 x T-Beam, 868 MHz, board V1.1 (RAK811 boards to be added later) |
| Channel plan | EU868 |

## Software

ChirpStack installed natively on Raspberry Pi OS with apt. No Docker. The end nodes are flashed from the Arduino IDE.

## Setup

1. Flash Raspberry Pi OS to the Pi and boot it.
2. Enable SPI on the Pi.
3. Wire the RAK831 to the Pi over SPI and give it its own 5V supply.
4. Install the packet forwarder and point it at the ChirpStack gateway bridge.
5. Install ChirpStack (network server, application server, gateway bridge) from apt.
6. Set the region to EU868 and register the gateway by its EUI.
7. Flash the T-Beams from the Arduino IDE and join them to the network.
8. Confirm uplinks are arriving in the ChirpStack web interface.
9. Mount the gateway on the roof once UCC Network Section approval comes through.

## Measurement plan

Zones run vertically through every accessible floor, which gives a floor-by-zone grid of 24 cells.

- 1 anchor point per cell, 24 in total
- 6 mobile points per cell, 144 in total
- 168 measurement positions overall
- 100 uplinks per point per spreading factor
- Full SF7 to SF12 sweep at the anchor point of each cell
- Single spreading factor elsewhere, to stay inside duty cycle limits

Four end nodes are rotated through the six zones rather than left in place. Node identity is recorded with every point.

## How zones are defined

Zones are not drawn by room or floor. They are drawn by structural depth: how many walls the signal crosses, whether the path is line of sight, how many courtyards it crosses, and how far it is from the gateway. This maps onto the COST 231 multi-wall model and ITU-R P.1238.

## Research framing

Radio wave propagation theory is the theoretical framework. Design Science Research is the methodology.

## Current status

Mapping is done. Data collection is waiting on gateway mounting permission from the UCC Network Section.
