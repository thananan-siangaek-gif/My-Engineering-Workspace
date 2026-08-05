# Learning Journey: 4-Switch Synchronous Buck-Boost Converter

> "The expert in anything was once a beginner."

This document records the developer's learning journey from having no prior knowledge of Power Electronics to designing and building a functional converter. It serves as a personal reference, a problem-solving log, and a resource for others who may follow a similar path.

**Purpose:**
- Rebuild and solidify electrical engineering fundamentals from first principles.
- Document encountered problems and their resolutions to avoid repeating mistakes.
- Create a reference for personal review and for others interested in the subject.

**Current Status:** Phase 1 & 2 Core Concepts Completed. Proceeding to Component Selection and Simulation.
**Started:** July 20, 2026
**Target Deadline:** August 17, 2026

---

## Learning Roadmap

The learning sequence is designed for someone with a weak foundation who needs to complete a functional project within a constrained timeline.

### Phase 0: Circuit Fundamentals (Week 1)
- [x] Voltage (V), Current (I), Resistance (R), and Ohm's Law
- [x] Kirchhoff's Voltage Law (KVL) -- verified through LAB 01
- [x] Kirchhoff's Current Law (KCL) -- verified through analytical problem solving
- [x] Electrical Power (P = VI, P = I^2 R) and thermal dissipation
- [x] Series-parallel circuits and voltage dividers

### Phase 1: Semiconductor Basics (Week 1-2)
- [x] Diodes: Forward/Reverse bias, forward voltage (Vf) -- verified through LAB 01
- [ ] N-Channel MOSFET: Vgs(th), Rds(on), gate charge (Qg)
- [ ] Why an STM32 cannot drive a MOSFET directly

### Phase 2: Energy Storage Components (Week 2)
- [x] Inductor (L): V = L * di/dt, "current preserver"
- [x] Capacitor (C): I = C * dv/dt, "voltage preserver"
- [x] Time constants and ripple trade-offs
- [x] Sizing L and C based on specified ripple and worst-case scenarios

### Phase 3: DC-DC Converter Principles (Week 2-3)
- [ ] PWM and duty cycle
- [ ] Buck Converter: operating principle, waveforms, equations
- [ ] Boost Converter: operating principle, waveforms, equations
- [ ] 4-Switch Buck-Boost: three operating modes
- [ ] Volt-second balance and charge balance

### Phase 4: Practical Implementation (Week 3-4)
- [ ] Gate drivers: bootstrap circuits, dead-time, shoot-through
- [ ] Reading datasheets (MOSFET, inductor, capacitor)
- [ ] PCB layout rules (high di/dt loops, grounding strategy)
- [ ] STM32 firmware: PWM generation, ADC with DMA, safety interlocks

---

## Learning Log

### Day 1 -- July 20, 2026

**Topics Covered:**
- Fundamentals of V, I, R and their relationship through Ohm's Law
- Electrical power and thermal dissipation (P = I^2 R)
- Hydraulic analogy for building intuition about circuits
- Distinction between Ohmic and Non-Ohmic devices

**Key Takeaways:**
1. Voltage (V) represents the "pressure" that pushes charge through a circuit.
2. Current (I) represents the actual flow rate of charge.
3. Resistance (R) represents opposition to current flow.
4. Ohm's Law (V = IR) is not merely a formula; it describes a physical equilibrium that always holds for Ohmic devices.
5. Thermal dissipation scales with the square of current: doubling the current quadruples the heat. This has critical implications for component selection and thermal management.
6. Diodes are Non-Ohmic devices; their V-I relationship is non-linear and cannot be described by V = IR.

**Activities:**
- [x] Studied Ohm's Law and power dissipation theory
- [x] Discussed Ohmic vs Non-Ohmic device distinction

**Open Questions:**
- (none)

**Resources Used:**
- Instructor-led discussion on V, I, R fundamentals

---

### Day 2 -- July 24, 2026

**Topics Covered:**
- Diode forward bias and reverse bias behavior
- Kirchhoff's Voltage Law (KVL) verification in series circuits
- Practical measurement of diode forward voltage (Vf)
- Comparison between theoretical calculations and Proteus simulation results

**Key Takeaways:**
1. The forward voltage of a silicon diode (1N4148) is approximately 0.7V, but it varies slightly with current. Treating Vf as constant at 0.7V is an acceptable approximation for engineering calculations.
2. Kirchhoff's Voltage Law holds true: V_source = V_diode + V_resistor in all forward-bias configurations tested.
3. In reverse bias, the diode blocks current completely (I = 0), and the entire source voltage appears across the diode.
4. Schottky diodes (e.g., 1N5819) have a lower forward voltage (~0.3V), making them more efficient for power conversion applications.

**Activities:**
- [x] Completed LAB 01: Diode Characteristics and KVL Verification in Proteus
- [x] Recorded all results and circuit diagrams in PDF format

**Experimental Observations:**
- Reverse bias results matched theory perfectly: V_diode = 5.0V, V_resistor = 0V, I = 0mA.
- V_diode decreased slightly as resistance increased (lower current), confirming non-linear behavior.

**Resources Used:**
- Proteus simulation software
- LAB 01 guide (self-prepared)
- Full results documented in: docs/learning/LAB_01_Diode_KVL.pdf

---

### Day 3 -- July 28, 2026

**Topics Covered:**
- Kirchhoff's Current Law (KCL) and node analysis.
- Electrical power dissipation and thermal management (P = I^2 R).
- Series and parallel circuit analysis.
- Voltage divider design for microcontroller ADC interfacing.

**Key Takeaways:**
1. KCL is a direct application of the conservation of charge: the sum of currents entering a node equals the sum of currents leaving it.
2. Thermal dissipation scales with the square of the current (I^2). Doubling the current quadruples the heat, making low Rds(on) MOSFETs critical.
3. Synchronous rectification drastically reduces conduction losses, validating the choice of a synchronous topology for this project.
4. Voltage dividers are essential for scaling high converter voltages down to safe levels for the STM32 ADC (max 3.3V).

**Activities:**
- [x] Solved analytical KCL node problems to verify current flow logic.
- [x] Calculated power dissipation and junction temperatures for hypothetical MOSFETs.
- [x] Designed and calculated a voltage divider network for a 4.2V Li-ion battery sensing circuit.

**Resources Used:**
- First principles analytical problem solving (paper and pencil).
- Instructor-led guided calculations.

---

### Day 4 -- August 5, 2026

**Topics Covered:**
- Role of Inductor (L) and Capacitor (C) in smoothing PWM square waves into stable DC.
- Understanding Ripple (Delta V_out and Delta I_L) and its design trade-offs.
- Duty Cycle calculations for Buck (D = V_out / V_in) and Boost (D = 1 - V_in / V_out) modes.
- Worst-case scenario analysis for component sizing.
- Real-world component selection criteria (I_sat, DCR, ESR, Voltage Rating derating).

**Key Takeaways:**
1. L resists changes in current, while C resists changes in voltage. Together, they filter high-frequency switching into a stable DC output with minimal ripple.
2. A 30% ripple current (Delta I_L) is the industry-standard trade-off, balancing component size/cost against efficiency and transient response.
3. Worst-case ripple in Buck mode occurs at maximum V_in (20V). Components must be sized for this condition to ensure safe operation at lower input voltages.
4. Calculated values (L = 80uH, C = 15uF) must be rounded up to standard values (L = 100uH, C = 4x22uF MLCC + 100uF Electrolytic) to provide a necessary safety margin.
5. Real-world constraints dictate selection: Inductor Saturation Current (I_sat) must be >= 3A to prevent core saturation during load steps, and Capacitor Voltage Rating must be >= 25V (2x V_out) to handle voltage spikes and aging.

**Activities:**
- [x] Analyzed the "why" behind L and C sizing formulas, moving beyond rote calculation to first-principles understanding.
- [x] Calculated Duty Cycle, L, and C based on target specifications (V_in = 6-20V, V_out = 12V, I_out = 2A, f_sw = 100kHz).
- [x] Documented component selection criteria and trade-offs in personal study notes (Start Phase 1.pdf).

**Open Questions:**
- How to properly implement the bootstrap circuit for the high-side N-Channel MOSFETs in the actual hardware? (To be addressed in Phase 4).

**Resources Used:**
- Self-prepared summary notes and first-principles analysis (Start Phase 1.pdf).
- Instructor-led guided breakdown of component sizing logic.

---

## Issues and Solutions Log

A record of problems encountered and their resolutions, to prevent repeating the same mistakes.

| # | Date | Problem | Root Cause | Resolution | Status |
|---|------|---------|------------|------------|--------|
| 1 | - | (none yet) | - | - | - |

**Example Entry:**

| # | Date | Problem | Root Cause | Resolution | Status |
|---|------|---------|------------|------------|--------|
| 1 | 2026-07-25 | MOSFET failed during testing | Missing dead-time in PWM signal | Added 100 ns dead-time in TIM1 configuration | Resolved |
| 2 | 2026-07-27 | Output ripple exceeded specification | High ESR of output capacitor | Replaced with multiple MLCC capacitors in parallel | Resolved |

---

## Mistakes Log

Documented mistakes intended to serve as reminders for the author and as guidance for others.

### Mistake #1: [Title]
**What was done:**
**Result:**
**What should have been done:**
**Lesson learned:**

---

## Progress Tracker

| Phase | Topic | Started | Completed | Notes |
|-------|-------|---------|-----------|-------|
| 0 | Circuit Fundamentals | 2026-07-20 | 2026-07-28 | Fully completed. Solid foundation established. |
| 1 | Semiconductor Basics | 2026-07-24 | - | Diode LAB complete. Next: MOSFET parameters. |
| 2 | Energy Storage (L, C) | 2026-08-05 | 2026-08-05 | Sizing, ripple trade-offs, and selection criteria understood. |
| 3 | DC-DC Converters | - | - | |
| 4 | Practical Implementation | - | - | |

---

## Resource Library

### Tools
- **Proteus** -- Circuit simulation (assigned by instructor)
- **LTspice** -- Advanced simulation (backup)
- **EasyEDA** -- PCB design
- **STM32CubeIDE** -- Firmware development

---

## Final Reflection

(To be written upon project completion)

**What went well:**
-

**What could be improved:**
-

**Most important lesson learned from this project:**
-

---

*Last updated: August 5, 2026*