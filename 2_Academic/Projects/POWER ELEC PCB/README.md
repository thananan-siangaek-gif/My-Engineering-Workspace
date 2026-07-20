# Digitally-Assisted 4-Switch Synchronous Buck-Boost Converter with STM32 Control

## Project Overview
This project presents the design, mathematical modeling, simulation, and hardware-in-the-loop 
implementation of a high-efficiency **Non-Inverting 4-Switch Synchronous Buck-Boost Converter** 
built entirely from discrete components. Designed as an advanced academic laboratory project, 
this system is capable of both stepping down (bucking) and stepping up (boosting) a wide range 
of DC input voltages to a stable, regulated DC output voltage **with the same polarity**.

Rather than relying on integrated monolithic converter chips, this system implements a discrete 
power stage driven by dedicated half-bridge gate drivers. An **STM32 microcontroller** acts as 
the intelligent central host, overseeing real-time telemetry (voltage/current sensing), system 
protection, and user-interface control.

### Target Specifications

| Parameter | Value |
|-----------|-------|
| Input Voltage ($V_{in}$) | 6V – 20V (e.g., single-cell Li-ion to 5S LiPo) |
| Output Voltage ($V_{out}$) | Regulated 12V (adjustable via UI) |
| Maximum Output Current ($I_{out}$) | 2A continuous |
| Switching Frequency ($f_{sw}$) | 100 kHz |
| Output Voltage Ripple ($\Delta V_{out}$) | < 50 mV peak-to-peak |
| Inductor Current Ripple ($\Delta I_L$) | 30% of $I_{out\_max}$ |

---

## Technical Specifications & Topology

The system utilizes a **Non-Inverting 4-Switch Synchronous Buck-Boost** topology, consisting 
of four N-channel MOSFETs arranged in two half-bridges, a single power inductor ($L$), and 
input/output filter capacitors ($C_{in}$ and $C_{out}$).

### Switching States by Operating Mode

| Mode | $Q_1$ (Buck HS) | $Q_2$ (Buck LS) | $Q_3$ (Boost HS) | $Q_4$ (Boost LS) |
|------|:---:|:---:|:---:|:---:|
| **Buck** | PWM | Complement | OFF (or ON) | ON |
| **Boost** | ON | OFF | Complement | PWM |
| **Buck-Boost Transition** | PWM | Complement | PWM | PWM |

### Power Stage Equations (Steady-State, Ideal)

Unlike the inverting buck-boost, the **non-inverting** 4-switch topology uses two independent 
duty cycles ($D_{buck}$ and $D_{boost}$):

*   **Buck Mode** ($V_{in} > V_{out}$):
    $$V_{out} = D_{buck} \times V_{in}$$
    where $Q_1$ switches at duty cycle $D_{buck}$, $Q_2$ operates complementarily (with dead-time), 
    and $Q_4$ remains ON as a synchronous rectifier.

*   **Boost Mode** ($V_{in} < V_{out}$):
    $$V_{out} = \frac{V_{in}}{1 - D_{boost}}$$
    where $Q_3$ switches at duty cycle $D_{boost}$, $Q_4$ operates complementarily, and $Q_1$ 
    remains ON to pass current directly to the output.

*   **Buck-Boost Transition Mode** ($V_{in} \approx V_{out}$):
    Both half-bridges modulate simultaneously to smoothly transfer between modes without 
    output voltage discontinuity.

### Component Sizing Equations

*   **Power Inductor ($L$):**
    $$L = \frac{V_{out} \cdot (1 - D_{buck})}{f_{sw} \cdot \Delta I_L} \quad \text{(worst case in Buck mode)}$$

*   **Output Capacitor ($C_{out}$):**
    $$C_{out} \geq \frac{\Delta I_L}{8 \cdot f_{sw} \cdot \Delta V_{out}}$$

---

## System Architecture

### 1. Discrete Power Stage
*   **4× N-Channel MOSFETs** selected for low $R_{DS(on)}$ and low gate charge ($Q_g$).
*   **2× Half-Bridge Gate Drivers** (e.g., IR2104 / NCP5181) with **built-in dead-time** 
    to prevent shoot-through — critical for hardware safety.
*   **Bootstrap circuit** for high-side MOSFET gate drive.
*   **RC Snubber network** across each switch node to damp ringing.

### 2. STM32 Host Controller
*   **PWM Generation:** Hardware TIMers with complementary outputs and configurable dead-time.
*   **High-Speed Telemetry:** Multi-channel ADC sampling $V_{out}$ and $I_{out}$ via DMA 
    at ≥ 10 kSPS.
*   **Digital Control Loop:** PI/PID compensator running at $f_{sw}$ to regulate $V_{out}$.
*   **Hardware Safety Interlock:** Background diagnostics monitor OVP, OCP, and OTP. On fault, 
    the STM32 pulls the gate driver *Shutdown (SD)* pin LOW within microseconds.
*   **User Interface:** I²C-driven OLED/LCD for setpoint adjustment and telemetry display.

---

## Development Roadmap & Milestones

### Phase 1: Research, Design, and CAD Layout (Deadline: August 17, 2026)
*   [ ] Finalize mathematical models for $L$, $C_{out}$, and input/output capacitors.
*   [ ] Run LTspice simulations: steady-state, transient load step, and mode transition.
*   [ ] Design schematic and route PCB in **EasyEDA** following strict layout rules:
    *   Wide traces / copper pours for high-current paths.
    *   Minimize the high-$di/dt$ loop area (Input Cap → MOSFETs → GND).
    *   Separate **PGND** and **AGND**, joined at a single star point.
    *   Place gate drivers adjacent to MOSFET gates; place bootstrap components close to driver IC.
*   [ ] Generate Gerber/BOM files and submit to JLCPCB.

### Phase 2: STM32 Firmware Development
*   [ ] Configure TIMers for complementary PWM with hardware dead-time.
*   [ ] Implement ADC + DMA for continuous multi-channel sampling.
*   [ ] Develop state-machine for smooth Buck ↔ Boost transitions.
*   [ ] Implement digital PI compensator (fixed-point or floating-point).
*   [ ] Program safety interlocks and I²C display driver.

### Phase 3: Assembly, Debugging, and Empirical Validation
*   [ ] Progressive bring-up with current-limited bench supply:
    1.  Verify gate-drive waveforms (no shoot-through, clean edges).
    2.  Low-voltage, light-load test (e.g., 5V / 100 mA).
    3.  Nominal voltage and load testing.
*   [ ] Capture switching node waveforms, rise/fall times, and output ripple with oscilloscope.
*   [ ] Document results for the final laboratory report.

---

## Bill of Materials (Key Components)

| Item | Example Part | Purpose |
|------|--------------|---------|
| MCU | STM32F401 / STM32G431 | Control & telemetry |
| MOSFETs | CSD18540Q5B / IRFZ44N | Power switches |
| Gate Driver | IR2104 / NCP5181 | Level-shift & dead-time |
| Inductor | 10 µH, 4A sat | Energy storage |
| Output Caps | 4× 22µF MLCC + 100µF electrolytic | Output filter |
| Current Sense | INA240 / shunt + op-amp | $I_{out}$ measurement |
| Display | SSD1306 OLED (I²C) | User interface |

---

## Safety Notes
This project involves high-$di/dt$ switching and potentially destructive fault conditions. 
Always use a current-limited power supply during bring-up, keep a fire extinguisher nearby, 
and never probe high-$dv/dt$ nodes with unisolated equipment.
