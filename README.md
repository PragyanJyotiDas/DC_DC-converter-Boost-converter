# Boost Converter Waveform Analysis

**Course Project — Power Electronics**
**Set:** B &nbsp;|&nbsp; **Batch:** G2

## Group Members
| Name | Roll No. |
|---|---|
| Adithya G | 240005004 |
| Sagnik Deb | 240002062 |
| Pragyan Jyoti Das | 240002055 |
| P Havish | 240002047 |

---

## Problem Statement

Design and implement a DC-DC boost converter with the following specifications, and study its inductor current and switch voltage waveforms in Continuous Conduction Mode (CCM), and demonstrate the transition to Discontinuous Conduction Mode (DCM) by increasing load resistance.

| Parameter | Value |
|---|---|
| Input Voltage | 14 V |
| Output Voltage | 20 V |
| Switching Frequency | 7.5 kHz |
| Output Current | 1 A |

---

## Basic Definitions

- **CCM (Continuous Conduction Mode):** Inductor current never reaches zero during a switching cycle.
- **DCM (Discontinuous Conduction Mode):** Inductor current becomes zero for part of the cycle (light load).
- **BCM (Boundary Conduction Mode):** The boundary between CCM and DCM, where the inductor current just touches zero.

---

## Experimental Setup

A PWM gate driver drives the power MOSFET switch. The boost converter power stage consists of an inductor, a fast-recovery diode, an output capacitor, and a resistive load — the standard boost topology below:

```
Vin (14V) ──── L ──┬──────────────────► D (QH08TZ600) ──┬──── Vout (20V)
                    │                                     │
                 MOSFET                                  Cout      R_load
              (IRFZ44N)                                    │          │
                    │                                     GND ─────GND
                   GND
```

The MOSFET gate is switched by a TL494-based PWM controller board, buffered through a TC4426A gate driver, at the design switching frequency of 7.5 kHz.

---

## Hardware / Components Used

| Component | Part Number | Role | Key Ratings |
|---|---|---|---|
| PWM Controller | **TL494** | Generates the switching signal; on-chip oscillator (set by RT/CT) fixes switching frequency; error amplifiers allow feedback-based duty-cycle control | V_CC: 7–40 V, f_OSC up to 300 kHz |
| Gate Driver | **TC4426A** | Buffers the low-current PWM logic signal into a fast, high-current gate drive for the power MOSFET | 1.5 A peak output, ~25 ns switching into 1000 pF |
| Power MOSFET | **IRFZ44N** (IRFZ44NPbF) | Main switching element (S1) of the boost converter | V_DSS = 55 V, R_DS(on) = 17.5 mΩ, I_D = 49 A |
| Boost Diode | **QH08TZ600** (Qspeed H-Series) | Fast, soft-recovery rectifier diode that delivers inductor current to the output during the MOSFET off-time | V_RRM = 600 V, I_F(avg) = 8 A, low Q_RR (~25.5 nC) for reduced switching loss |

**Driver Circuit:** A PWM driver board (EMPEL LiTi V2.3) generates the gate-drive signal, which is applied to the MOSFET through a breadboard test setup with a toroidal inductor.

**Simulation:** `simulation1.slx` — Simulink model of the boost converter, used to cross-check the hardware-measured waveforms against theoretical behavior.

---

## Passive Components

| Component | Hardware Value | Simulation Value (`simulation1.slx`) |
|---|---|---|
| Boost Inductor (L) | **1 mH** | 4.5 mH |
| Output Capacitor (C) | **220 µF** | 25 µF |

> Note: the Simulink model uses different L and C values (4.5 mH, 25 µF) than the physical hardware build (1 mH, 220 µF) — likely an earlier design iteration that wasn't updated to match the final hardware.

---

## Driver Output (Measured)

| Quantity | Measured Value |
|---|---|
| Switching Frequency | ≈ 7.13 kHz |
| Duty Cycle | ≈ 31.7% |
| Peak Gate Voltage (V_pk) | ≈ 13 V |

*(Target duty cycle for 14V → 20V conversion: D ≈ 1 − V_in/V_out ≈ 30%, consistent with the measured value.)*

---

## Waveforms & Observations

Oscilloscope captures of inductor current and MOSFET switch voltage were taken under three load conditions:

| Condition | Load Resistance | Observation |
|---|---|---|
| **BCM** | — | Inductor current just reaches zero at the end of each switching cycle |
| **CCM** | 20 Ω | Continuous inductor current, higher magnitude |
| **CCM (light load)** | 40 Ω | Continuous but reduced current, approaching DCM |

*(See report / images for the corresponding oscilloscope screenshots.)*

---

## Conclusion

Lower load resistance (higher output current demand) maintains Continuous Conduction Mode, while increasing load resistance progressively reduces the inductor current, shifting operation toward the Boundary and then Discontinuous Conduction Modes. The observed waveforms are consistent with the theoretical behavior of a boost converter operated in CCM/DCM.

---

## Repository Contents

- `report_dc_dc_converter.pdf` — Full waveform analysis report with oscilloscope captures
- `simulation1.slx` — Simulink simulation model of the boost converter
- `IRFZ44NPBF.PDF` — Power MOSFET datasheet
- `QH08TZ600.PDF` — Boost diode datasheet
- `TC4426A.PDF` — Gate driver datasheet
- `tl494.pdf` — PWM controller datasheet
