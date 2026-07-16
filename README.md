# Basic-PLC-programming-Conveyor-Belt-Counter

---
Introduction

This project provides a functional control simulation for a standard conveyor system designed to handle material transport workflows efficiently, it incorporates integrated timing mechanisms and a digital counter to manage the sequential movement and accumulation of items by utilizing a software-based PLC application (CODESYS with IEC 61131-3), the setup eliminates the immediate need for physical hardware during the initial development phase.
Overall, it serves as a foundational baseline application for studying automated material tracking and process sequencing.

---

Usage 

- This application is primarily utilized to automate the starting, stopping, and directional tracking of a conveyor belt mechanism.
- The system tracks the throughput of products or packages moving along the line via an integrated counting block.
- It utilizes specialized timers to introduce precise operational delays, control dwell times, and prevent mechanical wear from abrupt changes.
- Operators can use it to simulate real-world industrial scenarios, such as package sorting, bottleneck management, and production line buffering.
- Additionally, the configuration allows for safety monitoring by establishing defined interval checks for moving components.

---

Programming Language

- The project is programmed using the IEC 61131-3 standard languages natively supported by the CODESYS environment.
- Code components typically rely on standard graphical or textual formats such as Ladder Diagram (LD) or Function Block Diagram (FBD) for control logic.
- Complex sequential operations and timer evaluations may also be structured using Structured Text (ST) script blocks.
- The system leverages built-in IEC library functions, including standard timer blocks (TON/TOF) and counter modules (CTU/CTD) to execute its core instructions.
- These programming formats ensure that the logic remains highly portable, legible, and easily maintainable for automation engineers.

---

How to Activate

- To activate the project, you must first launch the CODESYS Development System application on a compatible workstation.
- Next, load the main project file into the environment to restore the programmatic structure and device tree parameters.
- You then navigate to the online menu and select the simulation mode option to activate the virtual PLC environment.
- After enabling the simulation, compile the code to verify that no active bugs or syntax errors exist within the program files.
- Finally, perform a login operation to download the compiled control logic directly into the active application runtime target.

---



How to Run the Visualization

- The project includes a dedicated visualization manager designed to render a graphical human-machine interface (HMI) for operators.
- To launch this screen, locate the visualization object within the application tree view inside your CODESYS workspace.
- Ensure the project is actively running online, as the interface depends on the CallTask__VISU_TASK execution loop.
- Press the start button or F5 key within the development environment to begin the active cyclical execution of the visual elements.
- The graphical display will then allow you to interactively toggle control inputs, view the conveyor state, and monitor counter values in real time.



---


Where Can It Be Used

- This specific setup is highly suitable for educational laboratories focused on teaching industrial automation and programmable logic controllers.
- It can be implemented in packaging facilities to manage automated sorting systems or product distribution lines.
- Manufacturing plants can deploy similar logic to regulate material handling tasks between different production assembly stations.
- Warehouses can utilize these concepts to optimize the flow of cargo containers through automated fulfillment zones.
- Ultimately, it serves as an excellent low-risk staging script for prototyping large-scale industrial distribution networks.

---

Demonstration Video



https://github.com/user-attachments/assets/4fc26c51-3ca6-47d5-bece-e15409adaf13





