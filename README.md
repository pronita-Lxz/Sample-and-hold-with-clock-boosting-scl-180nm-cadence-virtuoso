# Sample-and-hold-with-clock-boosting-scl-180nm-cadence-virtuoso
A full-custom implementation of a clock-bootstrapped Sample and Hold (S&H) circuit designed and simulated in **Cadence Virtuoso** using the **SCL 180nm PDK** with 3.3V thick-oxide MOSFETs. Input from a photo diode, output goes to the comparators of a 5bit SAR ADC. 
This circuit is part of the front-end design for a **5-bit Flash ADC** being developed under the **MeitY Chips to Startup (C2S) project** at NEHU, Shillong.
## Table of Contents

- [Overview](#overview)
- [Circuit Architecture](#circuit-architecture)
- [Design Parameters](#design-parameters)
- [Simulation Results](#simulation-results)
- [Repository Structure](#repository-structure)
- [Tools Used](#tools-used)
- [References](#references)
## Overview

A Sample and Hold circuit is a critical building block in any ADC. The main challenge in low-voltage CMOS design is that the threshold voltage (Vtn) of the sampling switch limits rail-to-rail operation and increases on-resistance (ron).

This design uses a **clock bootstrapping technique** to boost the gate drive voltage of the sampling NMOS switch beyond Vdd, ensuring:

- Constant and low ron across the full input voltage range
- Rail-to-rail sampling capability
- Clean hold phase with minimal droop

The design is based on the single-switch bootstrapped S&H topology (Fig. 7) from:

> Aneesh K, Manoj G, *"Sample and Hold Circuit with Clock Boosting"*, ICPSC 2021.

Adapted and implemented for **3.3V supply using SCL 180nm thick-oxide devices**.

---

## Circuit Architecture

The circuit consists of two sub-blocks:

### 1. Clock Bootstrapping Network (T1–T4 + C1, C2)

| Device | Type | Role |
|--------|------|------|
| T1 | PMOS | Connects Vdd to top plate of C1 during hold phase |
| T2 | PMOS | Connects Vdd to top plate of C2 during hold phase |
| T3 | NMOS | Passes boosted voltage to T5 gate during sample phase |
| T4 | NMOS | Discharges T5 gate to GND during hold phase |
| INV | Inverter | Generates CLKbar from CLK |

**Operation:**
- **Hold phase (CLK = 0):** C1 charges to Vdd. T4 pulls T5 gate to GND, turning the switch OFF.
- **Sample phase (CLK = 1):** C1 charge transfers through C2, boosting T5 gate to ~Vdd + Vin, ensuring strong turn-on regardless of input level.

### 2. Sampling Switch + Hold Capacitor (T5 + Cout)

| Device | Type | Role |
|--------|------|------|
| T5 | NMOS | Main sampling switch |
| Cout | MIM Cap | Hold capacitor, stores sampled voltage |

---

## Design Parameters

### Technology

| Parameter | Value |
|-----------|-------|
| Process | SCL 180nm |
| Device type | 3.3V thick-oxide MOSFET |
## Transistor Sizing

| Device | Type | W (µm) | L (µm) |
|--------|------|--------|--------|
| T5 (sampling switch) | NMOS | 0.3 | 0.35 |
| T4 (gate discharge) | NMOS | 0.3 | 0.35 |
| T1, T2 (bootstrap PMOS) | PMOS | 42 | 0.35 |
| T3 (bootstrap NMOS) | NMOS | 85 | 0.35 |
| INV PMOS | PMOS | 42 | 0.35 |
| INV NMOS | NMOS | 85 | 0.35 |

### Capacitor Values

| Capacitor | Value | Purpose |
|-----------|-------|---------|
| C1 | 7 pF | Bootstrap energy reservoir |
| C2 | 5 pF | Boost coupling capacitor |
| Cout | 10 pF | Hold / sampling capacitor |

**Design ratio:** C1 : C2 : Cout = 1.4 : 1 : 2

### Input Signal (Simulation)

| Parameter | Value |
|-----------|-------|
| Input frequency | 100 Hz |
| Input amplitude | 125 mVpp |
| Input DC offset | 1.65V (Vdd/2) |
| Sampling clock frequency | 2 kHz |
| Clock swing | 0 to 3.3V |

---

## Simulation Results

Simulated in **Cadence Spectre**, transient analysis over 6ms.

| Parameter | Result |
|-----------|--------|
| Supply voltage | 3.3V |
| Sampling frequency | 2 kHz |
| Input frequency | 100 Hz |
| Output swing | ~1.52V to ~2.48V (matches Vin) |
| Hold droop | Minimal (flat hold steps observed) |
| Rail-to-rail sampling | Achieved |

### Waveform Description

- CLK: clean 0 to 3.3V square wave at 2kHz
- Vin: 100Hz sinusoid centered at 1.65V
- Vout: clean staircase waveform faithfully tracking Vin envelope with flat hold steps

> Waveform screenshots are in `/simulation/waveforms/`

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Cadence Virtuoso | Schematic entry |
| Cadence Spectre | Transient simulation |
| SCL 180nm PDK | 3.3V thick-oxide device models |
| Cadence ADE L | Simulation environment |

---

## References

1. Aneesh K, Manoj G, *"Sample and Hold Circuit with Clock Boosting"*, 2021 3rd International Conference on Signal Processing and Communication (ICPSC), IEEE, 2021. DOI: 10.1109/ICSPC51351.2021.9451640

2. Hong, H.-C., Lee, G.-M., *"A 65-fJ/conversion-step 0.9-V 200-kS/s rail-to-rail 8-bit successive approximation ADC"*, IEEE Journal of Solid-State Circuits, vol. 42, pp. 2161–2168, 2007.

3. Abo, A.M., Gray, P.R., *"A 1.5-V, 10-bit, 14.3-MS/s CMOS pipeline analog-to-digital converter"*, IEEE Journal of Solid-State Circuits, vol. 34, pp. 599–606, 1999.

---

## Project Context

This S&H circuit is the **front-end block** of a 5-bit full-custom Flash ADC being designed for tapeout on SCL 180nm under the **MeitY Chips to Startup (C2S)** funded project at the **North Eastern Hill University (NEHU), Shillong**.
*Designed and simulated by Pronita Bora — NEHU Shillong, June 2026*
| Supply voltage (Vdd) | 3.3V |
| Simulator | Cadence Spectre |
