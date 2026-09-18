# 5-Qubit Quantum Simulator 

## Project Overview
This project focuses on designing and implementing a scalable, hardware-accelerated 5-Qubit Quantum Computing Simulator. By leveraging the parallel processing capabilities of an FPGA alongside an STM32 microcontroller host, this system acts as an embedded research sandbox to simulate and visually represent fundamental quantum mechanics principles, specifically Quantum Superposition and Quantum Entanglement.

The 5-Qubit implementation serves as the initial hardware platform and the starting point of a long-term scalability study. The architecture is designed to be progressively extended toward higher Qubit counts, with 20 Qubits ($2^{20} = 1,048,576$ complex state dimensions) serving as a long-term exploration target.

The objective is not simply to increase the number of Qubits, but to investigate how FPGA architecture, memory requirements, computational workload, latency, and hardware resource utilization change as the simulated quantum state grows exponentially.

---

## Core Architecture and Workload Division
To optimize computing resources and hardware execution efficiency, the system architecture is divided into two main processing units:

*   **FPGA (Hardware Accelerator):** Responsible for executing high-speed, parallel matrix computations representing quantum gates (e.g., Hadamard, CNOT) and managing the state vectors. A scope of 5 Qubits ($2^5 = 32$ complex state dimensions) was strategically chosen as the initial hardware implementation target, providing a practical platform for developing and validating the architecture.

*   **STM32 (System Host and Controller):** Manages user control interfaces, handles system input conditions, processes measurement triggers, and decodes final quantum state configurations for visualization.

As the Qubit count increases, the architecture will be progressively optimized to address the exponential growth of the state vector. Potential areas of investigation include parallel processing, pipelining, memory architecture, fixed-point arithmetic, FPGA resource sharing, and eventually multi-FPGA state-vector partitioning.

---

## Visual Representation: The Quantum Light Bulb Experiment
Instead of relying solely on digital display monitors, the project implements a physical LED Matrix Array to provide a raw, hardware-driven visual representation of quantum states:

*   **Superposition Mode:** Before a measurement occurs, the state vector elements are represented by multiple LEDs dimming uniformly (driven by high-frequency PWM signals from the FPGA), illustrating the probability distribution across quantum states.

*   **Measurement Mode (Wavefunction Collapse):** Upon triggering a measurement via the control interface, the system simulates wavefunction collapse. The state instantly reduces to a single outcome, causing one specific LED to illuminate at 100% brightness while others turn off.

*   **Entanglement Mode:** The system simulates entangled states (such as Bell States or GHZ States). When a measurement is triggered, the physical outputs of specific LEDs correspond directly to correlated quantum states, demonstrating quantum entanglement in real-time.

The physical visualization will remain part of the system as the simulator scales, while the underlying hardware architecture may evolve according to the computational and memory limitations encountered at higher Qubit counts.

---

## System Control Features
*   **Scalable Qubit Selector:** A hardware switch interface allows the user to dynamically adjust the simulation scope from 1 to 5 Qubits, facilitating live benchmarking of system latency and resource allocation changes.

*   **Preset Entanglement Randomizer:** To manage hardware complexity while maintaining operational stability, the controller utilizes a selection of predetermined quantum circuit configurations. Pressing the randomization interface switches between these pre-calculated entanglement patterns.

The scalable Qubit concept is also intended to become a foundation for future hardware scaling beyond the initial 5-Qubit implementation. Each increase in Qubit count will be treated as an engineering experiment to identify the practical limits of the current architecture before introducing further optimization or distributed hardware.

---

## Research Methodology and Future IEEE Publication Plans
The final phase of this R&D roadmap focuses on moving from development boards to a fully integrated custom hardware solution and systematically evaluating the scalability of the architecture:

1.  **Custom PCB Fabrication:** Design and route a high-speed digital circuit board to interface the FPGA and STM32, ensuring signal integrity across the data buses. Fabrication will be handled via dedicated manufacturing services in China.

2.  **Oscilloscope Laboratory Validation:** Utilize professional laboratory oscilloscope equipment to probe high-speed hardware control buses, capture clock alignment data, measure operational latency, and log physical execution waveforms.

3.  **Scalability Benchmarking:** Measure FPGA resource utilization, memory requirements, operating frequency, latency, and throughput as the Qubit count increases. The results will be used to identify architectural bottlenecks and determine where optimization or multi-FPGA approaches become necessary.

4.  **Academic Reporting:** The collected hardware execution data, resource utilization metrics, scalability benchmarks, and physical waveforms will serve as primary empirical evidence, structured to meet academic research standards for future publication under the IEEE format.

The long-term goal is to explore the practical boundary of FPGA-based quantum simulation rather than simply reaching a predefined Qubit count.

**Q5 is the starting point. Q20 is the exploration target. The limitations discovered between them are part of the research.**
