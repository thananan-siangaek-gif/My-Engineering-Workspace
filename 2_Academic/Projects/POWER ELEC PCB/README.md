# Digitally-Assisted 4-Switch Synchronous Buck-Boost Converter with STM32 Control

## Project Overview
This project presents the design, mathematical modeling, simulation, and hardware implementation of a high-efficiency Non-Inverting 4-Switch Synchronous Buck-Boost Converter built entirely from discrete components. Designed as an advanced academic laboratory project, this system is capable of both stepping down (bucking) and stepping up (boosting) a wide range of DC input voltages to a stable, regulated DC output voltage with the same polarity.

Rather than relying on integrated monolithic converter chips, this system implements a discrete power stage driven by dedicated half-bridge gate drivers. An STM32 microcontroller acts as the intelligent central host, overseeing real-time telemetry, system protection, and user-interface control.

> **Note:** This document serves as a high-level executive summary. For detailed learning logs, component selection trade-offs, datasheet analysis, and daily progress tracking, please refer to the [Learning Journey](./LEARNING_JOURNEY.md).

---

## Target Specifications

| Parameter | Value |
|-----------|-------|
| Input Voltage ($V_{in}$) | Wide range (e.g., single-cell Li-ion to multi-cell LiPo) |
| Output Voltage ($V_{out}$) | Regulated DC (adjustable via UI) |
| Maximum Output Current ($I_{out}$) | Continuous rated current |
| Switching Frequency ($f_{sw}$) | ~100 kHz |
| Output Voltage Ripple ($\Delta V_{out}$) | < 50 mV peak-to-peak |
| Inductor Current Ripple ($\Delta I_L$) | ~30% of $I_{out\_max}$ |

---

## Technical Specifications & Topology

The system utilizes a Non-Inverting 4-Switch Synchronous Buck-Boost topology, consisting of four N-channel MOSFETs arranged in two half-bridges, a single power inductor ($L$), and input/output filter capacitors ($C_{in}$ and $C_{out}$).

### Switching States by Operating Mode

| Mode | $Q_1$ (Buck HS) | $Q_2$ (Buck LS) | $Q_3$ (Boost HS) | $Q_4$ (Boost LS) |
|------|:---:|:---:|:---:|:---:|
| **Buck** | PWM | Complement | OFF (or ON) | ON |
| **Boost** | ON | OFF | Complement | PWM |
| **Buck-Boost Transition** | PWM | Complement | PWM | PWM |

### Power Stage Equations (Steady-State, Ideal)
*   **Buck Mode** ($V_{in} > V_{out}$): $V_{out} = D_{buck} \times V_{in}$
*   **Boost Mode** ($V_{in} < V_{out}$): $V_{out} = \frac{V_{in}}{1 - D_{boost}}$

### Component Sizing Equations
*   **Power Inductor ($L$):** $L = \frac{V_{out} \cdot (1 - D_{buck})}{f_{sw} \cdot \Delta I_L}$ (worst case in Buck mode)
*   **Output Capacitor ($C_{out}$):** $C_{out} \geq \frac{\Delta I_L}{8 \cdot f_{sw} \cdot \Delta V_{out}}$

---

## System Architecture

### 1. Discrete Power Stage
*   **4x N-Channel MOSFETs:** Selected for low $R_{DS(on)}$ and low gate charge ($Q_g$).
*   **2x Half-Bridge Gate Drivers:** Featuring built-in dead-time to prevent shoot-through, ensuring hardware safety.
*   **Bootstrap Circuit:** For high-side N-channel MOSFET gate drive.
*   **RC Snubber Network:** Across each switch node to damp high-frequency ringing.

### 2. STM32 Host Controller
*   **PWM Generation:** Hardware timers with complementary outputs and configurable dead-time.
*   **High-Speed Telemetry:** Multi-channel ADC sampling $V_{out}$ and $I_{out}$ via DMA.
*   **Digital Control Loop:** PI compensator running to regulate $V_{out}$.
*   **Hardware Safety Interlock:** Background diagnostics monitor OVP, OCP, and OTP. On fault, the STM32 pulls the gate driver Shutdown (SD) pin LOW.
*   **User Interface:** I2C-driven OLED for setpoint adjustment and telemetry display.

---

## Development Phases

### Phase 1: Research, Design, and CAD Layout
*   Mathematical modeling and component sizing based on worst-case scenarios.
*   Circuit simulation (Proteus/LTspice) for steady-state and transient analysis.
*   Schematic capture and PCB layout in EasyEDA, adhering to strict high-$di/dt$ loop and grounding rules.
*   Gerber/BOM generation and submission for fabrication.

### Phase 2: STM32 Firmware Development
*   Timer configuration for complementary PWM with hardware dead-time.
*   ADC and DMA implementation for continuous multi-channel sampling.
*   State-machine development for smooth Buck to Boost transitions.
*   Implementation of digital PI compensator and safety interlocks.

### Phase 3: Assembly, Debugging, and Empirical Validation
*   Progressive hardware bring-up using a current-limited bench supply.
*   Oscilloscope capture of switching node waveforms, rise/fall times, and output ripple.
*   Final documentation and laboratory report compilation.

---

## Bill of Materials (Key Component Categories)

| Item Category | Generic Description | Purpose |
|---------------|---------------------|---------|
| Microcontroller | ARM Cortex-M4 MCU (e.g., STM32 series) | Control, telemetry, and safety interlocks |
| Power Switches | Logic-Level N-Channel MOSFETs (4x) | Synchronous buck-boost power stage |
| Gate Drivers | Half-Bridge Drivers with built-in dead-time (2x) | Safe and efficient MOSFET switching |
| Energy Storage | Power Inductor & Low-ESR Capacitors | Current smoothing and output voltage filtering |
| Current Sensing | Current Sense Amplifier + Shunt Resistor | Real-time output current measurement |
| User Interface | I2C OLED/LCD Display | Setpoint adjustment and telemetry display |

---

## Safety Notes
This project involves high-$di/dt$ switching and potentially destructive fault conditions. Always use a current-limited power supply during bring-up, keep a fire extinguisher nearby, and never probe high-$dv/dt$ nodes with unisolated equipment.