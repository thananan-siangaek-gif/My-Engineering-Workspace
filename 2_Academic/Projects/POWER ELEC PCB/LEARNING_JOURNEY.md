# Learning Journey: 4-Switch Synchronous Buck-Boost Converter

> "The expert in anything was once a beginner."

This document records the developer's learning journey from having no prior knowledge of Power Electronics to designing and building a functional converter. It serves as a personal reference, a problem-solving log, and a resource for others who may follow a similar path.

**Purpose:**
- Rebuild and solidify electrical engineering fundamentals from first principles.
- Document encountered problems and their resolutions to avoid repeating mistakes.
- Create a reference for personal review and for others interested in the subject.

**Current Status:** Component Selection Complete. Initial Simulation Attempted. Pausing to refine control logic and component calculations before deep simulation/layout.
**Started:** July 20, 2026
**Target Deadline:** November 1, 2026 (Adjusted from August 17 mock stress-test deadline)

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
- [x] N-Channel MOSFET: Vgs(th), Rds(on), gate charge (Qg) -- verified via IRLZ44N datasheet
- [x] Why an STM32 cannot drive a MOSFET directly -- understood via IR2104 gate driver selection

### Phase 2: Energy Storage Components (Week 2)
- [x] Inductor (L): V = L * di/dt, "current preserver"
- [x] Capacitor (C): I = C * dv/dt, "voltage preserver"
- [x] Time constants and ripple trade-offs
- [x] Sizing L and C based on specified ripple and worst-case scenarios

### Phase 3: DC-DC Converter Principles (Week 2-3)
- [x] PWM and duty cycle
- [x] Buck Converter: operating principle, waveforms, equations
- [x] Boost Converter: operating principle, waveforms, equations
- [x] 4-Switch Buck-Boost: three operating modes
- [ ] Volt-second balance and charge balance

### Phase 4: Practical Implementation (Week 3-4)
- [x] Gate drivers: bootstrap circuits, dead-time, shoot-through -- IR2104S selected
- [x] Reading datasheets (MOSFET, inductor, capacitor) -- verified 4 main components
- [ ] PCB layout rules (high di/dt loops, grounding strategy)
- [ ] STM32 firmware: PWM generation, ADC with DMA, safety interlocks

---

## Learning Log

### Day 1 -- July 20, 2026
*(Previous entries retained for continuity...)*

### Day 6 -- August 17, 2026
*(Mock deadline retrospective and datasheet cross-verification retained...)*

---

### Day 7 -- August 25, 2026

**Topics Covered:**
- Initial 4-Switch Buck-Boost circuit assembly and open-loop simulation in Proteus.
- Observing transient response and steady-state behavior with estimated component values (C_IN = C_OUT = 100µF).
- Troubleshooting unexpected output voltages (2.44V at 50% duty cycle, 0.41V at 20% duty cycle).
- Identifying the critical importance of defined control logic (pass-through states) for the inactive half-bridge.

**Key Takeaways:**
1. **Transient vs. Steady-State:** Initial simulation showed an overshoot to ~10V before settling, highlighting the effect of large capacitance (100µF) on the transient response time. The system takes longer to stabilize but eventually reaches a steady state.
2. **Control Logic is King:** Applying a 50% PWM to *both* half-bridges simultaneously resulted in incorrect output (2.44V instead of the expected 6V). In pure Buck mode, the Boost half-bridge must be held in a static "pass-through" state (Q3 ON, Q4 OFF), not actively switched. Failing to define this causes the circuit to behave unpredictably.
3. **Iterative Troubleshooting:** Reducing the duty cycle to 20% and verifying pin states helped isolate that the high-side MOSFET was likely not turning on properly, pointing towards a need to rigorously verify the bootstrap circuit and logic inputs before proceeding.
4. **Pause and Calculate:** Recognized that blindly wiring without fully calculating the non-fixed values (like bootstrap capacitor and gate resistors) and defining the exact state machine leads to confusion. It is more efficient to pause, calculate, and define the logic than to guess in simulation.

**Activities:**
- [x] Assembled the 4-Switch topology with IRLZ44N and IR2104S in Proteus.
- [x] Ran initial open-loop tests with V_in = 12V and observed output settling at 2.44V (instead of expected 6V).
- [x] Troubleshot by reducing duty cycle to 20% and checking gate drive signals, observing a drop to 0.41V.
- [x] Decided to pause deep simulation to properly calculate bootstrap components and define the exact PWM control logic for each mode.

**Open Questions:**
- What is the exact static logic state (High/Low) required for the inactive bridge's IR2104S during pure Buck or pure Boost modes to ensure a clean pass-through?
- What is the precise calculation for the bootstrap capacitor ($C_{boot}$) given $Q_g = 47nC$ and a 100kHz switching frequency?

**Resources Used:**
- Proteus Circuit Simulation
- IR2104S Datasheet (Timing and Logic Thresholds)

---

## Issues and Solutions Log

| # | Date | Problem | Root Cause | Resolution | Status |
|---|------|---------|------------|------------|--------|
| 1 | 2026-08-11 | Confused V_GS(max) with V_GS(th) in IRLZ44N Datasheet | Rushed into thermal calculations before understanding basic operating parameters | Clarified that +/-16V is absolute max rating, while 4-5V is the operating condition for low Rds(on) | Resolved |
| 2 | 2026-08-11 | 100uH Inductor (MSS1260-104ML) had insufficient I_sat (1.88A) | Rigid adherence to calculated theoretical values without checking real-world availability | Pivoted to MSS1260-473ML (47uH, I_sat = 3.38A) and accepted slightly higher ripple | Resolved |
| 3 | 2026-08-17 | Unclear if STM32 3.3V logic can reliably drive IR2104S | Did not verify logic input thresholds against MCU output levels | Verified V_IH min = 3V for IR2104S; STM32 VOH min is sufficient | Resolved |
| 4 | 2026-08-25 | Simulation output settled at 2.44V instead of expected 6V (at 50% duty) | Applied PWM to both half-bridges simultaneously instead of using a static pass-through state for the inactive bridge | Paused simulation to define strict state-machine logic for Buck/Boost modes before re-simulating | In Progress |

---

## Mistakes Log

### Mistake #1: Confusing Absolute Maximum Ratings with Operating Conditions
**What was done:** Read the IRLZ44N datasheet and saw V_GS = +/-16V, then confused this with the voltage needed to drive the MOSFET.
**Result:** Temporary confusion about whether STM32 (3.3V) + IR2104 could properly drive the MOSFET.
**What should have been done:** First identify the difference between Absolute Maximum Ratings (limits that destroy the device) and Operating Conditions (values for proper function), then look at R_DS(on) test conditions.
**Lesson learned:** Always read the "Recommended Operating Conditions" and "Static Electrical Characteristics" tables before jumping to conclusions from the "Absolute Maximum Ratings" table.

### Mistake #2: Top-Down Design Without Checking Availability
**What was done:** Calculated ideal component values (100uH inductor) first, then searched for parts.
**Result:** Wasted time discovering the ideal part did not meet I_sat requirements or was unavailable.
**What should have been done:** Check component availability and critical constraints (I_sat, DCR, package) first, then adapt calculations.
**Lesson learned:** Bottom-up design (verify parts first, then calculate) is more efficient for time-constrained projects.

### Mistake #3: Simulating Without Defined Control Logic
**What was done:** Wired the 4-switch circuit in Proteus and applied generic PWM signals to both gate drivers without defining the specific mode (Buck vs. Boost) logic.
**Result:** Unpredictable output voltage (2.44V) and wasted troubleshooting time.
**What should have been done:** Define the exact truth table for Q1-Q4 in Buck mode (e.g., Q3=High, Q4=Low for pass-through) before running the simulation.
**Lesson learned:** Simulation is only as good as the control logic fed into it. Define the state machine on paper first.

---

## Progress Tracker

| Phase | Topic | Started | Completed | Notes |
|-------|-------|---------|-----------|-------|
| 0 | Circuit Fundamentals | 2026-07-20 | 2026-07-28 | Fully completed. Solid foundation established. |
| 1 | Semiconductor Basics | 2026-07-24 | 2026-08-11 | Diode LAB complete. MOSFET and Gate Driver verified via datasheets. |
| 2 | Energy Storage (L, C) | 2026-08-05 | 2026-08-11 | Sizing, ripple trade-offs, and selection criteria understood. Inductor pivoted to 47uH. |
| 3 | DC-DC Converters | 2026-08-05 | 2026-08-25 | PWM, Buck, Boost, 4-Switch modes understood. Initial simulation attempted, identified need for strict control logic. |
| 4 | Practical Implementation | 2026-08-11 | - | Datasheets verified. Schematic capture and PCB layout pending. |

---

## Resource Library

### Datasheets
- **IRLZ44N** -- N-Channel Logic Level MOSFET, TO-220 (docs/datasheets/IRLZ44N-MOSFET.pdf)
- **IR2104S** -- Half-Bridge Gate Driver with 520ns Dead-time, SOIC-8 (docs/datasheets/IR2104(S)-Half-Bridge Gate.PDF)
- **INA240A2** -- Current Shunt Monitor, 50 V/V Gain, Enhanced PWM Rejection, TSSOP-8 (docs/datasheets/INA240-Current Sensor.PDF)
- **STM32F401CD** -- ARM Cortex-M4 MCU, 84 MHz, 384KB Flash, 96KB RAM, LQFP-64 (docs/datasheets/STM32F401CD-STM32.PDF)
- **MSS1260-473ML** -- Coilcraft Shielded SMT Power Inductor, 47uH, I_sat = 3.38A (docs/datasheets/MSS1260-473ML.pdf)

### Study Notes
- **Start Phase 1** -- Personal notes on L/C sizing, ripple trade-offs, and worst-case analysis (docs/learning/Start Phase 1.pdf)
- **LAB 01** -- Diode Characteristics and KVL Verification in Proteus (docs/learning/LAB_01_Diode_KVL.pdf)

### Tools
- **Proteus** -- Circuit simulation (assigned by instructor)
- **LTspice** -- Advanced simulation (backup)
- **EasyEDA** -- PCB design and schematic capture
- **STM32CubeIDE** -- Firmware development
- **JLCPCB** -- PCB fabrication and assembly

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

*Last updated: August 25, 2026*