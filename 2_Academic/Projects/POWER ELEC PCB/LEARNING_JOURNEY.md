# Learning Journey: 4-Switch Synchronous Buck-Boost Converter

> "The expert in anything was once a beginner."

This document records the developer's learning journey from having no prior knowledge of Power Electronics to designing and building a functional converter. It serves as a personal reference, a problem-solving log, and a resource for others who may follow a similar path.

**Purpose:**
- Rebuild and solidify electrical engineering fundamentals from first principles.
- Document encountered problems and their resolutions to avoid repeating mistakes.
- Create a reference for personal review and for others interested in the subject.

**Current Status:** Component Selection Complete (MCU upgraded to STM32F411CE). Entering Schematic Capture Phase.
**Started:** July 20, 2026
**Target Deadline:** August 17, 2026 (Mock deadline used as planning stress-test; actual university deadline: November 1, 2026)

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
- [x] MCU selection finalized: STM32F411CE (upgraded from STM32F401CD)
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

### Day 6 -- August 17, 2026

**Topics Covered:**
- Comprehensive review and cross-verification of all four primary component datasheets.
- Final confirmation of system compatibility between STM32F401, IR2104S, IRLZ44N, and INA240A2.
- Mock deadline retrospective: evaluating the 1-month sprint plan and identifying bottlenecks.

**Key Takeaways:**
1. **STM32F401 Verification:** Confirmed the MCU has one Advanced-Control Timer (TIM1) with complementary PWM outputs and programmable dead-time -- exactly what is needed to drive the IR2104S safely. The 12-bit ADC (2.4 MSPS) is sufficient for voltage and current sensing via DMA.
2. **IR2104S Verification:** Confirmed logic input threshold (V_IH min = 3V) is compatible with STM32's 3.3V logic output. Built-in dead-time of 520ns (typical) is sufficient to prevent shoot-through for IRLZ44N (which has typical turn-on/turn-off times in the tens of nanoseconds range). Undervoltage lockout (V_CC UV+ = 8.9V) ensures the driver will not operate with insufficient gate drive voltage.
3. **IRLZ44N Verification:** Confirmed Logic-Level MOSFET with V_GS(th) = 1-2V and R_DS(on) = 22 mOhm at V_GS = 4V. Total gate charge Q_g = 47nC is well within the drive capability of IR2104S (I_O+ = 130mA, I_O- = 270mA), ensuring fast switching transitions and minimal switching losses.
4. **INA240A2 Verification:** Confirmed enhanced PWM rejection (93-dB AC CMRR at 50 kHz) is critical for accurate current sensing in a 100 kHz switching converter. Wide common-mode range (-4V to 80V) allows flexible high-side or low-side shunt placement.
5. **System-Level Compatibility:** The entire signal chain is verified: STM32 (3.3V PWM) -> IR2104S (boosts to 10-15V gate drive with dead-time) -> IRLZ44N (fully enhanced at 4-5V) -> Power Stage. Current sensing via INA240A2 (1.0V output at 2A) feeds back into STM32 ADC safely.
6. **Mock Deadline Retrospective:** The 1-month sprint from July 20 to August 17 was intentionally aggressive to stress-test the learning plan. While the full prototype was not completed, the foundational knowledge (Phase 0-2) and component selection (Phase 1, 4) were solidified. The actual university deadline is November 1, 2026, providing ~2.5 months for schematic capture, PCB layout, fabrication, firmware, and bring-up.

**Activities:**
- [x] Re-read and annotated key sections of STM32F401CD datasheet (TIM1, ADC, pinout).
- [x] Re-read and annotated key sections of IR2104S datasheet (timing, logic thresholds, UVLO).
- [x] Re-read and annotated key sections of IRLZ44N datasheet (V_GS(th), R_DS(on), Q_g, SOA).
- [x] Re-read and annotated key sections of INA240A2 datasheet (PWM rejection, gain, common-mode range).
- [x] Cross-referenced all four datasheets to confirm system-level compatibility.
- [x] Conducted mock deadline retrospective and drafted revised execution plan for Aug 18 - Nov 1.

**Open Questions:**
- What is the optimal shunt resistor placement (high-side vs. low-side) for the INA240A2 in a 4-switch topology? (To be resolved during schematic capture).
- What bootstrap capacitor value is optimal for 100 kHz switching with IRLZ44N (Q_g = 47nC)? (To be calculated during schematic capture).

**Resources Used:**
- STM32F401CD Datasheet (STMicroelectronics, DocID025644 Rev 3)
- IR2104(S) Datasheet (International Rectifier, Data Sheet No. PD60046-S)
- IRLZ44N Datasheet (International Rectifier / Infineon)
- INA240 Datasheet (Texas Instruments, SBOS662)
- Start Phase 1.pdf (personal study notes)

---

### Day 7 -- August 25, 2026

**Topics Covered:**
- Initial 4-Switch Buck-Boost circuit assembly and open-loop simulation in Proteus.
- Observing transient response and steady-state behavior with estimated component values (C_IN = C_OUT = 100uF).
- Troubleshooting unexpected output voltages (2.44V at 50% duty cycle, 0.41V at 20% duty cycle).
- Identifying the critical importance of defined control logic (pass-through states) for the inactive half-bridge.

**Key Takeaways:**
1. **Transient vs. Steady-State:** Initial simulation showed an overshoot to ~10V before settling, highlighting the effect of large capacitance (100uF) on the transient response time. The system takes longer to stabilize but eventually reaches a steady state.
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

### Day 8 -- August 28, 2026

**Topics Covered:**
- Deep conceptual synthesis of the 4-Switch Non-Inverting Buck-Boost topology.
- Mapping theoretical switching states to practical use cases (battery regulation).
- Architecture exploration: evaluating ESP32-S3 and FPGA as alternative or complementary controllers.

**Key Takeaways:**
1. **Modular Mental Model of 4-Switch Topology:**
   The circuit is best understood not as one tangled schematic, but as two half-bridges sharing a common inductor:
   - **Buck Side (Q1 High-Side, Q2 Low-Side):** Generates the primary switching action. When Q1 is ON, current flows from V_in to charge the inductor (Q2 must be OFF to prevent shoot-through). When Q1 turns OFF, Q2 turns ON, allowing the inductor to act as a temporary power source and release energy toward the output node.
   - **Boost Side (Q3 High-Side, Q4 Low-Side):** Acts as the secondary stage or pass-through depending on the operating mode.

2. **Mode Logic Truth Table:**

| Mode | Q1 | Q2 | Q3 | Q4 | Who Generates Hz | Formula |
|------|:---:|:---:|:---:|:---:|:-----------------|:--------|
| Buck | 0/1 (PWM) | 0/1 (Comp.) | 0 (OFF) | 1 (ON) | Q1, Q2 | V_out = D * V_in |
| Boost | 1 (ON) | 0 (OFF) | 0/1 (PWM) | 0/1 (Comp.) | Q3, Q4 | V_out = V_in / (1 - D) |
| Buck-Boost Transition | 0/1 (PWM) | 0/1 (Comp.) | 0/1 (PWM) | 0/1 (Comp.) | All Q | Complex; avoid if possible |

   - In Buck mode, higher Duty Cycle (longer Q1 ON time) brings V_out closer to V_in.
   - In Boost mode, higher Duty Cycle (longer Q3 ON time) allows the inductor to store more energy, pushing V_out higher.

3. **Real-World Application Mapping (Battery Regulation):**
   The primary use case is maintaining a stable output (e.g., 12V) from a varying source like a battery:
   - Battery at 14V -> Use Buck mode to step down to 12V.
   - Battery at 10V -> Use Boost mode to step up to 12V.
   - Battery at 11.2V or 13.1V -> The controller smoothly transitions or uses hysteresis to maintain the 12V target without interruption.

4. **Hysteresis Strategy for Transition Mode:**
   The Buck-Boost transition mode is inherently complex and difficult to control due to rapid state changes. A practical firmware strategy is to implement a voltage hysteresis band (e.g., strict Buck above 13.5V, strict Boost below 11.5V) to avoid operating in the messy transition zone, thereby simplifying the control logic.

5. **ESP32-S3 Evaluation for Dashboard Control:**
   - **Pros:** Built-in Wi-Fi/Bluetooth, excellent for IoT dashboards, easy web server implementation.
   - **Cons:** Noisy ADC (non-linear in some ranges), no hardware Break Input for fast fault protection, PWM generation more complex and prone to jitter under Wi-Fi load.
   - **Verdict:** Not suitable as the primary Power Stage controller. However, excellent as a secondary Telemetry/Gateway MCU communicating with the primary STM32 via UART/SPI.

6. **FPGA Evaluation:**
   - **Pros:** True parallel processing, sub-nanosecond dead-time precision, fastest fault response (<100 ns), ideal for high-frequency converters (>500 kHz).
   - **Cons:** No built-in ADC (requires external), no FPU (requires fixed-point math), very steep learning curve (VHDL/Verilog), high cost (~1000 THB vs ~150 THB for STM32), high power consumption.
   - **Verdict:** Overkill for this 100 kHz project. Not recommended given the timeline and budget.

7. **Hybrid Architecture (STM32 + CPLD) -- Future Reference:**
   A middle-ground approach where STM32 handles the main control loop, ADC reading, and communication, while a small CPLD (e.g., Lattice MachXO2, ~200 THB) handles precise dead-time generation, fast fault protection, and PWM synchronization. This combines the best of both worlds but adds complexity.

**Activities:**
- [x] Synthesized datasheet knowledge into a cohesive mental model of the 4-switch topology.
- [x] Mapped theoretical switching states to practical battery regulation scenarios.
- [x] Conducted architecture trade-off analysis: STM32 vs ESP32-S3 vs FPGA vs Hybrid.
- [x] Documented findings for future reference and potential scope expansion.

**Open Questions:**
- What is the optimal hysteresis band width for Buck-Boost transition to avoid oscillation?
- If ESP32-S3 is added later, what communication protocol (UART vs SPI) minimizes latency for telemetry?

**Resources Used:**
- Personal study notes and conceptual synthesis
- Instructor-led discussion on architecture trade-offs

---

### Day 9 -- September 5, 2026

**Topics Covered:**
- MCU re-evaluation and upgrade decision: STM32F401CD -> STM32F411CE.
- Comparative datasheet analysis between STM32F401 and STM32F411 families.
- Understanding the significance of Voltage Scaling, ART Accelerator, and Batch Acquisition Mode (BAM).
- Finalizing the MCU choice for the project BOM.

**Key Takeaways:**
1. **Why Upgrade to STM32F411CE:**
   After comparing the STM32F401CD and STM32F411CE datasheets side-by-side, the F411CE offers meaningful advantages for this project without significant cost increase:
   - **Higher Clock Speed:** 100 MHz vs 84 MHz. This provides more CPU cycles per PWM period (1000 cycles at 100 kHz vs 840 cycles), giving more headroom for the PI control loop, ADC DMA handling, and safety interlock logic.
   - **More SRAM:** 128 KB vs 96 KB. Extra 32 KB is valuable for DMA double-buffering of ADC samples and future telemetry logging.
   - **Additional SPI/I2S:** 5 SPIs vs 4 SPIs. Provides more flexibility for future peripheral expansion (e.g., external ADC, SD card logging, or ESP32-S3 telemetry bridge).
   - **Batch Acquisition Mode (BAM):** A unique power-saving feature that allows DMA to transfer data directly to SRAM while the Flash and ART accelerator are powered down. Useful if battery-powered operation becomes a requirement later.
   - **Voltage Scale 1:** Supports 100 MHz operation with VOS[1:0] = 0x11, requiring V12 = 1.26-1.38V on VCAP pins.
   - **Higher I/O Toggle Speed:** Up to 100 MHz vs 84 MHz.
   - **Higher Junction Temperature:** 130°C vs 125°C (minor but useful margin).

2. **Trade-offs and Considerations:**
   - **Slightly Higher Power Consumption:** At 100 MHz, typical run current is ~20-21 mA (vs ~16-17 mA at 84 MHz for F401). For a bench-powered converter, this is negligible.
   - **Pin Compatibility:** The STM32F411CE in UFQFPN48 package is pin-compatible with STM32F401CE, so the PCB footprint remains the same. However, the VCAP capacitor value changes from 4.7uF (F401 single VCAP) to 4.7uF (F411 single VCAP) -- actually the same, but the Voltage Scale 1 requirement means VCAP must maintain 1.26-1.38V at 100 MHz.
   - **APB1 Clock:** Increased from 42 MHz to 50 MHz. This is beneficial for any future I2C or USART peripherals on APB1.
   - **Cost:** Price difference is minimal (~10-20 THB), well justified by the performance gain.

3. **Impact on Project Timeline:**
   - No delay. The STM32F411CE is readily available (e.g., STM32F411CEU6 on Black Pill boards, or UFQFPN48 chips from LCSC/JLCPCB).
   - STM32CubeIDE fully supports F411 with the same HAL libraries. Code written for F401 TIM1/ADC/DMA is 95% portable to F411.
   - The decision was made quickly after datasheet comparison, demonstrating improved datasheet literacy compared to Day 5.

4. **Final BOM MCU Specification:**
   - **Part Number:** STM32F411CEU6 (UFQFPN48, 512KB Flash, 128KB SRAM, 100 MHz)
   - **Alternative:** STM32F411RET6 (LQFP64) if more GPIOs are needed later.
   - **Key Peripherals Used:** TIM1 (Advanced Control, 4 complementary PWM channels with dead-time), ADC1 (12-bit, 2.4 MSPS, 16 channels), DMA1/DMA2 (16 streams total), I2C1 (for OLED), USART1 (for debug/ESP32 bridge).

**Mistakes and Lessons Learned:**
- **Initial Oversight:** Originally selected STM32F401CD based on familiarity and community support (Blue Pill ecosystem) without thoroughly comparing the F411 datasheet. This is another instance of "top-down" thinking -- assuming the first viable option is the best.
- **Lesson:** Always compare at least 2-3 candidate components in the same family before finalizing. The marginal cost increase often buys significant headroom that prevents redesign later.

**Activities:**
- [x] Downloaded and reviewed STM32F411xC/xE datasheet (DocID026289 Rev 6).
- [x] Created side-by-side comparison table of F401 vs F411 key parameters.
- [x] Verified pin compatibility between STM32F401CE and STM32F411CE (UFQFPN48).
- [x] Confirmed STM32F411CE availability on JLCPCB and LCSC.
- [x] Updated project BOM and README to reflect MCU upgrade.
- [x] Noted Voltage Scale 1 requirement for 100 MHz operation in schematic design checklist.

**Open Questions:**
- Should the system clock be configured at 100 MHz from day one, or start at 84 MHz (Voltage Scale 2) for initial bring-up stability and ramp up later?
- For the PI control loop running at 100 kHz, is fixed-point arithmetic sufficient, or should we leverage the F411's FPU for floating-point PID calculations?

**Resources Used:**
- STM32F411xC/xE Datasheet (STMicroelectronics, DocID026289 Rev 6)
- STM32F401xC/xE Datasheet (STMicroelectronics, DocID025644 Rev 3) -- for comparison
- STM32F411 Black Pill board reference schematics
- JLCPCB Basic Parts library search results

---

## Architecture Exploration Log

This section documents alternative architectures and expansion ideas considered during the project, preserved for future reference or scope expansion after the primary prototype is functional.

### Option A: ESP32-S3 as Telemetry Gateway (Recommended Expansion)
**Concept:** Keep STM32F411CE as the primary Power Controller. Add ESP32-S3 as a secondary Telemetry & Gateway MCU.
**Architecture:** `[Power Stage] <-> [STM32F411CE] <--UART/SPI--> [ESP32-S3] <--Wi-Fi--> [Web Dashboard]`
- STM32 retains clean ADC, fast fault response, and stable PWM.
- ESP32-S3 handles Wi-Fi stack without interfering with power control.
- Even if Wi-Fi crashes, the power stage continues operating safely.
**Cons:** Adds ~200 THB to BOM. Requires inter-MCU communication protocol design. Increases PCB complexity.
**Recommended Implementation Order:** Get STM32 + OLED working first (primary prototype). Add ESP32-S3 as a bonus feature for the final report.

### Option B: Full FPGA Replacement (Not Recommended)
**Concept:** Replace STM32 entirely with an FPGA (e.g., Xilinx Artix-7).
**Pros:** Sub-nanosecond dead-time precision, true parallel processing, ideal for high-frequency converters (>500 kHz).
**Cons:** No built-in ADC, no FPU, steep learning curve (VHDL/Verilog), high cost (~1000 THB), high power consumption.
**Verdict:** Overkill for a 100 kHz project. Not recommended given the timeline and budget.

### Option C: Hybrid STM32 + CPLD (Middle Ground)
**Concept:** STM32 handles main control loop and communication; small CPLD (e.g., Lattice MachXO2) handles precise dead-time generation, fast fault protection, and PWM synchronization.
**Pros:** Combines FPGA-level timing precision with MCU-level flexibility. CPLD cost is modest (~200 THB). Fault response <100 ns (hardware-level).
**Cons:** Adds design complexity. Requires learning basic CPLD development flow. Not necessary for 100 kHz operation (STM32 TIM1 is sufficient).
**Verdict:** Interesting for future high-frequency projects. Not necessary for this prototype.

---

## Issues and Solutions Log

| # | Date | Problem | Root Cause | Resolution | Status |
|---|------|---------|------------|------------|--------|
| 1 | 2026-08-11 | Confused V_GS(max) with V_GS(th) in IRLZ44N Datasheet | Rushed into thermal calculations before understanding basic operating parameters | Clarified that +/-16V is absolute max rating, while 4-5V is the operating condition for low Rds(on) | Resolved |
| 2 | 2026-08-11 | 100uH Inductor (MSS1260-104ML) had insufficient I_sat (1.88A) | Rigid adherence to calculated theoretical values without checking real-world availability | Pivoted to MSS1260-473ML (47uH, I_sat = 3.38A) and accepted slightly higher ripple | Resolved |
| 3 | 2026-08-17 | Unclear if STM32 3.3V logic can reliably drive IR2104S | Did not verify logic input thresholds against MCU output levels | Verified V_IH min = 3V for IR2104S; STM32 VOH min is sufficient | Resolved |
| 4 | 2026-08-25 | Simulation output settled at 2.44V instead of expected 6V (at 50% duty) | Applied PWM to both half-bridges simultaneously instead of using a static pass-through state for the inactive bridge | Paused simulation to define strict state-machine logic for Buck/Boost modes before re-simulating | In Progress |
| 5 | 2026-09-05 | Initial MCU selection (STM32F401CD) lacked headroom for future features | Selected first familiar option without comparing alternatives in the same family | Upgraded to STM32F411CE after datasheet comparison; gained 100 MHz clock, 128KB SRAM, 5th SPI | Resolved |

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

### Mistake #4: Selecting the First Viable MCU Without Comparison
**What was done:** Chose STM32F401CD based on familiarity (Blue Pill ecosystem) without comparing the STM32F411CE datasheet.
**Result:** Missed out on 100 MHz clock, 32KB extra SRAM, 5th SPI, and BAM feature that would have provided valuable headroom.
**What should have been done:** Compare at least 2-3 candidate MCUs in the same family (F401 vs F411 vs F412) before finalizing.
**Lesson learned:** The marginal cost difference (~10-20 THB) between MCU variants often buys significant performance headroom that prevents redesign later. Always do a quick datasheet comparison before committing.

---

## Progress Tracker

| Phase | Topic | Started | Completed | Notes |
|-------|-------|---------|-----------|-------|
| 0 | Circuit Fundamentals | 2026-07-20 | 2026-07-28 | Fully completed. Solid foundation established. |
| 1 | Semiconductor Basics | 2026-07-24 | 2026-08-11 | Diode LAB complete. MOSFET and Gate Driver verified via datasheets. |
| 2 | Energy Storage (L, C) | 2026-08-05 | 2026-08-11 | Sizing, ripple trade-offs, and selection criteria understood. Inductor pivoted to 47uH. |
| 3 | DC-DC Converters | 2026-08-05 | 2026-08-28 | PWM, Buck, Boost, 4-Switch modes understood. Conceptual synthesis complete. Initial simulation attempted. |
| 4 | Practical Implementation | 2026-08-11 | - | Datasheets verified. Architecture options explored. MCU upgraded to STM32F411CE. Schematic capture and PCB layout pending. |

---

## Resource Library

### Datasheets
- **IRLZ44N** -- N-Channel Logic Level MOSFET, TO-220 (docs/datasheets/IRLZ44N-MOSFET.pdf)
- **IR2104S** -- Half-Bridge Gate Driver with 520ns Dead-time, SOIC-8 (docs/datasheets/IR2104(S)-Half-Bridge Gate.PDF)
- **INA240A2** -- Current Shunt Monitor, 50 V/V Gain, Enhanced PWM Rejection, TSSOP-8 (docs/datasheets/INA240-Current Sensor.PDF)
- **STM32F411CE** -- ARM Cortex-M4 MCU, 100 MHz, 512KB Flash, 128KB SRAM, UFQFPN48 (docs/datasheets/STM32F411CE-STM32.PDF)
- **STM32F401CD** -- ARM Cortex-M4 MCU, 84 MHz, 384KB Flash, 96KB SRAM, LQFP-64 (docs/datasheets/STM32F401CD-STM32.PDF) -- kept for reference
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

*Last updated: September 5, 2026*
