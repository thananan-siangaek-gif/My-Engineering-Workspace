# Learning Journey: 4-Switch Synchronous Buck-Boost Converter

> "The expert in anything was once a beginner."

This document records the developer's learning journey from having no prior knowledge of Power Electronics to designing and building a functional converter. It serves as a personal reference, a problem-solving log, and a resource for others who may follow a similar path.

**Purpose:**
- Rebuild and solidify electrical engineering fundamentals from first principles.
- Document encountered problems and their resolutions to avoid repeating mistakes.
- Create a reference for personal review and for others interested in the subject.

**Current Status:** Active Learning
**Started:** July 20, 2026
**Target Deadline:** August 17, 2026

---

## Learning Roadmap

The learning sequence is designed for someone with a weak foundation who needs to complete a functional project within a constrained timeline.

### Phase 0: Circuit Fundamentals (Week 1)
- [x] Voltage (V), Current (I), Resistance (R), and Ohm's Law
- [x] Kirchhoff's Voltage Law (KVL) -- verified through LAB 01
- [ ] Kirchhoff's Current Law (KCL)
- [ ] Electrical Power (P = VI, P = I^2 R) and thermal dissipation
- [ ] Series-parallel circuits and voltage dividers

### Phase 1: Semiconductor Basics (Week 1-2)
- [x] Diodes: Forward/Reverse bias, forward voltage (Vf) -- verified through LAB 01
- [ ] N-Channel MOSFET: Vgs(th), Rds(on), gate charge (Qg)
- [ ] Why an STM32 cannot drive a MOSFET directly

### Phase 2: Energy Storage Components (Week 2)
- [ ] Inductor (L): V = L * di/dt, "current preserver"
- [ ] Capacitor (C): I = C * dv/dt, "voltage preserver"
- [ ] Time constants (tau = L/R, tau = RC)
- [ ] Sizing L and C based on specified ripple

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
1. The forward voltage of a silicon diode (1N4148) is approximately 0.7V, but it varies slightly with current (typically 0.65V to 0.75V). For engineering calculations, treating Vf as constant at 0.7V is an acceptable approximation.
2. Kirchhoff's Voltage Law holds true: V_source = V_diode + V_resistor in all forward-bias configurations tested.
3. In reverse bias, the diode blocks current completely (I = 0), and the entire source voltage appears across the diode (V_diode = V_source).
4. Increasing the source voltage or decreasing the resistance increases the current, which causes a slight increase in V_diode (due to the non-linear diode characteristic).
5. Schottky diodes (e.g., 1N5819) have a lower forward voltage (~0.3V) compared to silicon diodes (~0.7V), making them more efficient for power conversion applications.

**Activities:**
- [x] Completed LAB 01: Diode Characteristics and KVL Verification in Proteus
- [x] Experiment 1: Forward bias with R = 100 ohm, V_source = 5V
- [x] Experiment 2: Varied resistance (100, 220, 470, 1000 ohm) with V_source = 5V
- [x] Experiment 3: Varied source voltage (1, 3, 5, 9, 12V) with R = 100 ohm
- [x] Experiment 4: Reverse bias test with V_source = 5V, R = 100 ohm
- [x] Recorded all results and circuit diagrams in PDF format

**Experimental Observations:**
- Experiment 1: V_diode measured slightly different from ideal 0.7V due to simulation model characteristics. KVL verified with minor deviation.
- Experiment 2: V_diode decreased slightly as resistance increased (lower current). This confirms the non-linear diode characteristic.
- Experiment 3: V_diode increased slightly as source voltage increased (higher current). Again, confirms non-linear behavior.
- Experiment 4: Reverse bias results matched theory perfectly: V_diode = 5.0V, V_resistor = 0V, I = 0mA.
- Experiment 5: Skipped to accelerate progress (Schottky diode comparison).

**Open Questions:**
- What causes the slight deviation between ideal and simulated V_diode values? (Hypothesis: simulation model includes more detailed diode characteristics)
- How does temperature affect diode forward voltage? (To be investigated later)

**Resources Used:**
- Proteus simulation software
- LAB 01 guide (self-prepared)
- Full results documented in: docs/learning/LAB_01_Diode_KVL.pdf

---

### Day 3 -- [Date]

**Topics Covered:**
- (to be filled after completion)

**Key Takeaways:**
- (to be filled after completion)

**Activities:**
- [ ] (planned activities)

**Open Questions:**
- (to be filled)

**Resources Used:**
- (to be added)

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
| 0 | Circuit Fundamentals | 2026-07-20 | - | Completed V, I, R, Ohm's Law, KVL. Next: KCL, Power |
| 1 | Semiconductor Basics | 2026-07-24 | - | Completed diode LAB. Next: MOSFET |
| 2 | Energy Storage (L, C) | - | - | |
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

*Last updated: July 24, 2026*
