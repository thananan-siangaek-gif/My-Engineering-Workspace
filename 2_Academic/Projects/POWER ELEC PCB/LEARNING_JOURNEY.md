# Learning Journey: 4-Switch Synchronous Buck-Boost Converter

> "The expert in anything was once a beginner."

This document records the developer's learning journey from having no prior knowledge of Power Electronics to designing and building a functional converter. It serves as a personal reference, a problem-solving log, and a resource for others who may follow a similar path.

**Purpose:**
- Rebuild and solidify electrical engineering fundamentals from first principles.
- Document encountered problems and their resolutions to avoid repeating mistakes.
- Create a reference for personal review and for others interested in the subject.

**Current Status:** Component Selection Complete. Proceeding to Schematic Capture in EasyEDA.
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

### Day 5 -- August 11, 2026

**Topics Covered:**
- Finalized component selection based on datasheet verification and real-world availability.
- Trade-off analysis for Inductor selection (prioritizing I_sat and DCR over nominal inductance value).
- Current sensing calculation and gain verification for STM32 ADC compatibility.
- Critical self-reflection on design workflow, datasheet literacy, and time management.

**Key Takeaways:**
1. **Design for Availability vs. Ideal Specs:** Initially targeted a 100uH inductor, but realized that rigid theoretical calculations often clash with real-world component availability and physical size constraints. Pivoted to a flexible strategy: identify available components with critical constraints (like I_sat >= 3A and low DCR) first, then recalculate and adapt flexible components (like R and C). This saved significant time and streamlined the BOM finalization.
2. **Component Sourcing Strategy:** Lack of experience led to a top-down design approach (calculate ideal values -> search for parts). The optimal workflow is bottom-up: verify critical component availability and datasheet constraints first, then adapt the circuit. Next time, I will dive into datasheets and part availability before deep theoretical calculations.
3. **Datasheet Literacy:** Felt overwhelmed by the sheer amount of jargon in datasheets, focusing only on immediate specs. Acknowledged the need to develop a systematic approach to reading datasheets (understanding test conditions, graphs, and secondary parameters) in future iterations.
4. **MOSFET Selection:** Confirmed IRLZ44N is ideal due to its Logic-Level V_GS(th) (1-2V) and low R_DS(on) (22 mOhm at 4V), ensuring it can be driven effectively by the 3.3V-compatible IR2104 gate driver.
5. **Gate Driver:** IR2104S (SOIC-8) is confirmed. It features built-in 520ns dead-time (preventing shoot-through) and accepts 3.3V logic directly from the STM32.
6. **Inductor Compromise:** Selected Coilcraft MSS1260-473ML (47 uH) instead of the calculated 100 uH. Reason: The 100 uH variant had insufficient saturation current (I_sat < 3A) and high DCR. The 47 uH variant guarantees I_sat = 3.38A and lower DCR (89 mOhm), which is safer for a 2A load, accepting a slightly higher ripple that can be managed by the output capacitors.
7. **Current Sensor:** Verified INA240A2 (Gain = 50 V/V). With a 10 mOhm shunt resistor at 2A max load, the output voltage will be 2A * 0.01 Ohm * 50 = 1.0V. This perfectly utilizes the lower third of the STM32's 3.3V ADC range, leaving ample headroom for overcurrent detection.

**Mistakes and Lessons Learned:**
- **Mistake:** Rushed into thermal and power calculations (P = I^2 R, T_junction) for the MOSFET without fully understanding its fundamental operating parameters first. This led to confusion when reading the V_GS specifications in the IRLZ44N datasheet (confusing Absolute Maximum Rating of +/-16V with the Operating Condition of 4-5V).
- **Lesson:** Always establish a solid understanding of basic operating parameters (V_GS(th), V_GS(max), R_DS(on) test conditions) before moving on to secondary calculations like power dissipation and thermal management.

**Activities:**
- [x] Reviewed and cross-referenced datasheets for IRLZ44N, IR2104S, INA240A2, and STM32F401.
- [x] Finalized the Bill of Materials (BOM) with specific part numbers.
- [x] Gathered all physical components for the project.
- [x] Conducted a self-review of the design process to identify workflow bottlenecks.

**Next Steps:**
- Begin Schematic Capture in EasyEDA.
- Focus on correct pinout mapping for IR2104S bootstrap circuit and INA240A2 shunt placement.

---

## Issues and Solutions Log

A record of problems encountered and their resolutions, to prevent repeating the same mistakes.

| # | Date | Problem | Root Cause | Resolution | Status |
|---|------|---------|------------|------------|--------|
| 1 | 2026-08-11 | Confused V_GS(max) with V_GS(th) in IRLZ44N Datasheet | Rushed into thermal calculations before understanding basic operating parameters | Clarified that +/-16V is absolute max rating, while 4-5V is the operating condition for low Rds(on) | Resolved |
| 2 | 2026-08-11 | 100uH Inductor (MSS1260-104ML) had insufficient I_sat (1.88A) | Rigid adherence to calculated theoretical values without checking real-world availability | Pivoted to MSS1260-473ML (47uH, I_sat = 3.38A) and accepted slightly higher ripple | Resolved |

---

## Mistakes Log

Documented mistakes intended to serve as reminders for the author and as guidance for others.

### Mistake #1: Confusing Absolute Maximum Ratings with Operating Conditions
**What was done:** Read the IRLZ44N datasheet and saw V_GS = +/-16V, then confused this with the voltage needed to drive the MOSFET.
**Result:** Temporary confusion about whether STM32 (3.3V) + IR2104 could properly drive the MOSFET.
**What should have been done:** First identify the difference between Absolute Maximum Ratings (limits that destroy the device) and Operating Conditions (values for proper function), then look at R_DS(on) test conditions.
**Lesson learned:** Always read the "Recommended Operating Conditions" and "Static Electrical Characteristics" tables before jumping to conclusions from the "Absolute Maximum Ratings" table.

---

## Progress Tracker

| Phase | Topic | Started | Completed | Notes |
|-------|-------|---------|-----------|-------|
| 0 | Circuit Fundamentals | 2026-07-20 | 2026-07-28 | Fully completed. Solid foundation established. |
| 1 | Semiconductor Basics | 2026-07-24 | 2026-08-11 | Diode LAB complete. MOSFET and Gate Driver verified via datasheets. |
| 2 | Energy Storage (L, C) | 2026-08-05 | 2026-08-11 | Sizing, ripple trade-offs, and selection criteria understood. Inductor pivoted to 47uH. |
| 3 | DC-DC Converters | 2026-08-05 | - | PWM, Buck, Boost, 4-Switch modes understood. Volt-second balance pending. |
| 4 | Practical Implementation | 2026-08-11 | - | Datasheets read. Gate driver selected. PCB layout and firmware pending. |

---

## Resource Library

### Datasheets
- **IRLZ44N** -- N-Channel Logic Level MOSFET (docs/datasheets/IRLZ44N-MOSFET.pdf)
- **IR2104S** -- Half-Bridge Gate Driver with Dead-time (docs/datasheets/IR2104(S)-Half-Bridge Gate.PDF)
- **INA240A2** -- Current Shunt Monitor, 50 V/V Gain (docs/datasheets/INA240-Current Sensor.PDF)
- **STM32F401** -- ARM Cortex-M4 MCU with FPU (docs/datasheets/STM32F401CD-STM32.PDF)
- **MSS1260** -- Coilcraft SMT Power Inductors (docs/datasheets/MSS1260-473ML.pdf)

### Study Notes
- **Start Phase 1** -- Personal notes on L/C sizing and ripple trade-offs (docs/learning/Start Phase 1.pdf)
- **LAB 01** -- Diode Characteristics and KVL Verification (docs/learning/LAB_01_Diode_KVL.pdf)

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

*Last updated: August 11, 2026*
