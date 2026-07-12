# 5-Qubit Quantum Simulator 

## Project Overview
This project focuses on designing and implementing a scalable, hardware-accelerated 5-Qubit Quantum Computing Simulator. By leveraging the parallel processing capabilities of an FPGA alongside an STM32 microcontroller host, this system acts as an embedded research sandbox to simulate and visually represent fundamental quantum mechanics principles, specifically Quantum Superposition and Quantum Entanglement.

---

## Core Architecture and Workload Division
To optimize computing resources and hardware execution efficiency, the system architecture is divided into two main processing units:

*   **FPGA (Hardware Accelerator):** Responsible for executing high-speed, parallel matrix computations representing quantum gates (e.g., Hadamard, CNOT) and managing the state vectors. A scope of 5 Qubits ($2^5 = 32$ complex state dimensions) was strategically chosen as the optimal sweet spot to maximize FPGA resource utilization without exceeding physical logic hardware limits.
*   **STM32 (System Host and Controller):** Manages user control interfaces, handles system input conditions, processes measurement triggers, and decodes final quantum state configurations for visualization.

---

## Visual Representation: The Quantum Light Bulb Experiment
Instead of relying solely on digital display monitors, the project implements a physical LED Matrix Array to provide a raw, hardware-driven visual representation of quantum states:

*   **Superposition Mode:** Before a measurement occurs, the state vector elements are represented by multiple LEDs dimming uniformly (driven by high-frequency PWM signals from the FPGA), illustrating the probability distribution across quantum states.
*   **Measurement Mode (Wavefunction Collapse):** Upon triggering a measurement via the control interface, the system simulates wavefunction collapse. The state instantly reduces to a single outcome, causing one specific LED to illuminate at 100% brightness while others turn off.
*   **Entanglement Mode:** The system simulates entangled states (such as Bell States or GHZ States). When a measurement is triggered, the physical outputs of specific LEDs correspond directly to correlated quantum states, demonstrating quantum entanglement in real-time.

---

## System Control Features
*   **Scalable Qubit Selector:** A hardware switch interface allows the user to dynamically adjust the simulation scope from 1 to 5 Qubits, facilitating live benchmarking of system latency and resource allocation changes.
*   **Preset Entanglement Randomizer:** To manage hardware complexity while maintaining operational stability, the controller utilizes a selection of predetermined quantum circuit configurations. Pressing the randomization interface switches between these pre-calculated entanglement patterns.

---

## Research Methodology and Future IEEE Publication Plans
The final phase of this R&D roadmap focuses on moving from development boards to a fully integrated custom hardware solution:

1.  **Custom PCB Fabrication:** Design and route a high-speed digital circuit board to interface the FPGA and STM32, ensuring signal integrity across the data buses. Fabrication will be handled via dedicated manufacturing services in China.
2.  **Oscilloscope Laboratory Validation:** Utilize professional laboratory oscilloscope equipment to probe high-speed hardware control buses, capture clock alignment data, measure operational latency, and log physical execution waveforms.
3.  **Academic Reporting:** The collected hardware execution data, resource utilization metrics, and physical waveforms will serve as primary empirical evidence, structured to meet academic research standards for future publication under the IEEE format.