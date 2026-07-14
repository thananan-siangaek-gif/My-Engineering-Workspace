# Digitally-Assisted 4-Switch Synchronous Buck-Boost Converter with STM32 Control

## Project Overview
This project presents the design, mathematical modeling, simulation, and hardware-in-the-loop implementation of a high-efficiency **4-Switch Synchronous Buck-Boost Converter** built entirely from discrete components. Designed as an advanced academic laboratory project, this system is capable of both stepping down (bucking) and stepping up (boosting) a wide range of DC input voltages to a stable, regulated DC output voltage.

Rather than relying on integrated monolithic converter chips, this system implements a discrete power stage driven by dedicated gate drivers. An **STM32 microcontroller** acts as the intelligent central host, overseeing real-time telemetry (voltage/current sensing), system protection, and user-interface control.

---

## Technical Specifications & Topology
The system utilizes a non-inverting **4-Switch Synchronous Buck-Boost** topology, consisting of four N-channel MOSFETs, a single power inductor ($L$), and input/output filter capacitors ($C_{in}$ and $C_{out}$). 

### Power Stage Equations
The ideal steady-state relationship between Output Voltage ($V_{out}$), Input Voltage ($V_{in}$), and Switching Duty Cycle ($D$) is governed by:

$$V_{out} = \frac{D}{1 - D} \times V_{in}$$

In operation:
*   **Buck Mode:** $Switches_{1,2}$ operate at high frequency while $Switch_{3}$ is permanently OFF and $Switch_{4}$ is permanently ON.
*   **Boost Mode:** $Switches_{3,4}$ operate at high frequency while $Switch_{1}$ is permanently ON and $Switch_{2}$ is permanently OFF.
*   **Buck-Boost Mode:** All four switches modulate dynamically to handle transition regions where $V_{in} \approx V_{out}$.

---

## System Architecture and Workload Division

*   **Discrete Power Stage:** Features 4 discrete N-Channel MOSFETs driven by two Half-Bridge Gate Driver ICs. 
*   **STM32 Host Controller:**
    *   **High-Speed Telemetry:** Samples output voltage and load currents via STM32's internal high-speed ADC utilizing Direct Memory Access (DMA).
    *   **Hardware Safety Interlock:** Runs background diagnostics. If an overvoltage, overcurrent, or thermal threshold is breached, the STM32 immediately pulls the gate driver *Enable (EN)* or *Shutdown (SD)* pins LOW, disabling all switching elements within microseconds to prevent hardware failure.
    *   **User Interface:** Operates an $I^2C$ LCD/OLED screen for system parameter visualization.

---

## Development Roadmap & Milestones

### Phase 1: Research, Design, and CAD Layout (Deadline: August 17, 2026)
*   Formulate mathematical models to calculate the optimal values for the power inductor ($L$) and output filter capacitor ($C_{out}$) under worst-case ripple current profiles.
*   Conduct SPICE-based simulations to analyze switching behavior, transient load responses, and potential Right Half Plane Zero (RHPZ) characteristics.
*   Design the system schematic and route the high-current physical PCB using the **EasyEDA** suite.
*   Enforce strict layout rules: Maintain wide traces for high-current paths, place decoupling/snubber circuits close to MOSFETs, and isolate the Power Ground (PGND) plane from the STM32 Analog Ground (AGND) to mitigate switching noise.
*   Generate fabrication outputs for direct deployment to Chinese fabrication facilities (JLCPCB) by the phase deadline.

### Phase 2: STM32 Firmware Development
*   Implement continuous ADC multi-channel scanning with hardware oversampling or software filtering algorithms.
*   Develop state-machine logic for smooth transitions between Buck and Boost operating regions.
*   Program the high-speed safety interlock routines and LCD visual drivers.

### Phase 3: Assembly, Debugging, and Empirical Validation
*   Assemble the manufactured PCB and perform progressive hardware bring-up tests using a current-limiting bench power supply.
*   Use an oscilloscope to capture switching node waveforms, rise/fall times, and transient output voltage ripple to include in the final laboratory report.