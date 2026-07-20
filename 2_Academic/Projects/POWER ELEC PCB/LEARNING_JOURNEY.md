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
- [ ] Kirchhoff's Laws (KVL, KCL)
- [ ] Electrical Power (P = VI, P = I^2 R) and thermal dissipation
- [ ] Series-parallel circuits and voltage dividers

### Phase 1: Semiconductor Basics (Week 1-2)
- [ ] Diodes: Forward/Reverse bias, forward voltage (Vf)
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

**Key Takeaways:**
1. Voltage (V) represents the "pressure" that pushes charge through a circuit.
2. Current (I) represents the actual flow rate of charge.
3. Resistance (R) represents opposition to current flow.
4. Ohm's Law (V = IR) is not merely a formula; it describes a physical equilibrium that always holds.
5. Thermal dissipation scales with the square of current: doubling the current quadruples the heat. This has critical implications for component selection and thermal management.

**Activities:**
- [ ] Simulate a diode + resistor circuit in Proteus (forward and reverse bias)
- [ ] Measure the forward voltage (Vf) of a 1N4148 diode
- [ ] Verify KVL: V_in = V_diode + V_resistor

**Open Questions:**
- (none yet)

**Resources Used:**
- (to be added)

---

### Day 2 -- [Date]

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
| 0 | Circuit Fundamentals | 2026-07-20 | - | Currently studying V, I, R |
| 1 | Semiconductor Basics | - | - | |
| 2 | Energy Storage (L, C) | - | - | |
| 3 | DC-DC Converters | - | - | |
| 4 | Practical Implementation | - | - | |

---

## Resource Library

### Books
- *Fundamentals of Power Electronics* by Robert W. Erickson (Chapters 3 and 6)
- (to be added)

### Videos
- Power Electronics by Sam Ben-Yaakov (YouTube)
- TI Precision Labs: Power Management (YouTube)
- (to be added)

### Articles and Application Notes
- SLYT405: Non-Isolated DC/DC Converters (Texas Instruments)
- (to be added)

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

*Last updated: July 20, 2026*
