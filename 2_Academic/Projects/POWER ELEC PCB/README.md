# Digitally-Assisted 4-Switch Synchronous Buck-Boost Converter with STM32 Control

## Project Overview
This project presents the design, mathematical modeling, simulation, and hardware implementation of a high-efficiency Non-Inverting 4-Switch Synchronous Buck-Boost Converter built entirely from discrete components. Designed as an advanced academic laboratory project, this system is capable of both stepping down (bucking) and stepping up (boosting) a wide range of DC input voltages to a stable, regulated DC output voltage with the same polarity.

Rather than relying on integrated monolithic converter chips, this system implements a discrete power stage driven by dedicated half-bridge gate drivers. An STM32 microcontroller acts as the intelligent central host, overseeing real-time telemetry, system protection, and user-interface control.

> **Note:** This document serves as a high-level executive summary. For detailed learning logs, component selection trade-offs, datasheet analysis, and daily progress tracking, please refer to the [Learning Journey](./LEARNING_JOURNEY.md).

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

## Safety Notes
This project involves high-$di/dt$ switching and potentially destructive fault conditions. Always use a current-limited power supply during bring-up, keep a fire extinguisher nearby, and never probe high-$dv/dt$ nodes with unisolated equipment.
