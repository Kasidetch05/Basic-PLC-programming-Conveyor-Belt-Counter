# Basic PLC Programming — Conveyor Belt Counter

A CODESYS V3.5 Ladder Diagram (LD) project that controls a conveyor belt motor and counts the boxes it carries. It uses a TON timer and a CTU counter, and flags the batch as done when the count reaches a preset value. The project runs on the CODESYS soft-PLC simulator and has a built-in visualization screen for operating and monitoring the line.

**Platform:** CODESYS V3.5 SP22 | **Language:** Ladder Diagram (IEC 61131-3) | **Runtime:** Simulation

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Requirements](#system-requirements)
- [Project Structure](#project-structure)
- [I/O and Variable List](#io-and-variable-list)
- [Control Logic](#control-logic)
- [Getting Started](#getting-started)
- [Testing the Program](#testing-the-program)
- [Possible Extensions](#possible-extensions)
- [Skills Demonstrated](#skills-demonstrated)

---

## Overview

Packaging lines often need to move products on a conveyor and group them into batches of a fixed size. This project models that process in a small form:

1. The operator starts the conveyor motor with a **Start** button and stops it with a **Stop** button.
2. While the belt runs, a **timer** produces a pulse each time a box passes, standing in for a box sensor.
3. A **counter** adds up the boxes.
4. When the count reaches the batch size, the **Batch_Done** signal turns on.
5. The operator clears the count with **Reset** / **Counter_Reset** to start the next batch.

## Features

- Start/Stop motor control with a seal-in (latching) circuit
- Box detection simulated with an on-delay timer (`TON`)
- Box counting with an up-counter (`CTU`) and an adjustable batch size (`PV`)
- `Batch_Done` output when the batch is complete
- Counter reset for the next batch
- HMI visualization screen (CODESYS Visualization) for buttons, lamps and the live count
- Runs entirely in simulation mode, so no physical PLC hardware is needed


## System Requirements

| Item | Version / Detail |
|---|---|
| IDE | CODESYS Development System V3.5 SP22 (or newer) |
| Runtime | Built-in simulation (`Online → Simulation`) or CODESYS Control Win V3 |
| Libraries | Standard 3.5.22.0, Visualization elements 4.9.x (added automatically) |
| OS | Windows 10 / 11 |

## Project Structure

```
CODESYS PROJECT/
├── Conveyor Belt with Timeers and Counter Basic 01.project   # Main CODESYS project (open this)
├── Conveyor Belt with Timeers and Counter Basic 01-*.opt     # IDE user/layout options
├── *.simulation.bootinfo / *.compileinfo                     # Generated build and simulation files
├── *_project.precompilecache                                 # Generated precompile cache
└── README.md
```

Inside the CODESYS project tree:

```

Device (CODESYS Control Win V3 – Simulation)
└── PLC Logic
    └── Application
        ├── Library Manager
        ├── LD_Conveyor_belt (PRG)     # Main ladder program
        ├── Visualization              # HMI screen
        ├── Visualization Manager
        └── Task Configuration
            └── MainTask → LD_Conveyor_belt
```

> **Note:** The `.bootinfo`, `.compileinfo`, `.precompilecache` and `.opt` files are created by CODESYS during builds. They can be regenerated and do not need to be under version control.

## I/O and Variable List

All variables are declared locally in `LD_Conveyor_belt`.

| Variable | Data Type | Role | Description |
|---|---|---|---|
| `Start` | `BOOL` | Input | Start push button. Starts the conveyor motor. |
| `Stop` | `BOOL` | Input | Stop push button. Stops the conveyor motor. |
| `Reset` | `BOOL` | Input | Reset push button for the motor control circuit. |
| `Motor` | `BOOL` | Output | Conveyor belt motor contactor. |
| `Box_Timer_On` | `TON` | Function block | On-delay timer that generates a pulse for each box while the belt runs. |
| `Box_Count` | `CTU` | Function block | Up-counter that counts boxes. `PV` sets the batch size. |
| `Counter_Reset` | `BOOL` | Input / internal | Resets `Box_Count` back to 0. |
| `Batch_Done` | `BOOL` | Output | Turns on when `Box_Count.CV >= PV` (batch complete). |


## Control Logic

The program is written as ladder rungs, which run every cycle of `MainTask`.

**Rung 1 — Motor start/stop (seal-in circuit)**

```
   Start        Stop                      Motor
 ──┤ ├──┬───────┤/├────────────────────────( )──
        │
   Motor│
 ──┤ ├──┘
```

Pressing `Start` energizes `Motor`. The parallel `Motor` contact keeps the motor running after `Start` is released. Pressing `Stop` breaks the circuit.

**Rung 2 — Box timer (simulated sensor)**

```
   Motor                 Box_Timer_On
 ──┤ ├──────────────────┤ TON        ├──
                        │ IN      Q  ├──
                        │ PT     ET  │
```

While the motor runs, `Box_Timer_On` times for its preset time (`PT`). Its `Q` output gives a pulse that stands for one box passing the sensor.

**Rung 3 — Box counter**

```
   Box_Timer_On.Q          Box_Count
 ──┤ ├──────────────────┤ CTU        ├──── Batch_Done
                        │ CU      Q  │
   Counter_Reset        │ RESET  CV  ├──── (current count)
 ──┤ ├──────────────────┤ PV         │
```

Each rising edge on `CU` adds 1 to `Box_Count.CV`. When `CV` reaches `PV`, `Q` turns on and drives `Batch_Done`. `Counter_Reset` sets `CV` back to 0.

### Timing summary

```
Start   ─┐_________________________________________
Motor    └─────────────────────────────────────────
Timer Q    _|‾|_____|‾|_____|‾|_____|‾|_____|‾|____
Count      0  1      2      3      4      5 = PV
Batch_Done ____________________________________|‾‾‾
```

## Getting Started

1. **Install CODESYS.** Download CODESYS Development System V3.5 (SP22 or newer) from the [CODESYS Store](https://store.codesys.com).
2. **Open the project.** Double-click `Conveyor Belt with Timeers and Counter Basic 01.project`, or use `File → Open Project` in CODESYS.
3. **Build.** Press `F11` (`Build → Build`) and check that the Messages window shows **0 errors**.
4. **Turn on simulation.** Select `Online → Simulation`.
5. **Log in and download.** Press `Alt + F8` (`Online → Login`) and accept the download prompt.
6. **Run.** Press `F5` (`Debug → Start`).
7. **Operate.** Open the **Visualization** object, or use the online view of `LD_Conveyor_belt` to force values with `Ctrl + F7`.

## Testing the Program

| # | Action | Expected Result |
|---|---|---|
| 1 | Set `Start` = TRUE, then FALSE | `Motor` turns on and stays on (seal-in). |
| 2 | Wait while the motor runs | `Box_Count.CV` goes up by 1 each time the timer finishes. |
| 3 | Let the count reach `PV` | `Batch_Done` turns TRUE. |
| 4 | Set `Stop` = TRUE | `Motor` turns off and counting stops. |
| 5 | Set `Counter_Reset` = TRUE | `Box_Count.CV` returns to 0 and `Batch_Done` turns FALSE. |
| 6 | Press `Start` again | A new batch starts from 0. |

## Possible Extensions

- Stop the motor automatically when `Batch_Done` turns on
- Replace the simulated timer pulse with a real photoelectric sensor input (`%IX`)
- Map the variables to physical I/O addresses (`%IX0.0`, `%QX0.0`, …) for a real PLC
- Add an emergency stop (E-Stop) circuit
- Log the number of finished batches and the total boxes
- Rewrite the logic in Structured Text (ST) to compare the two languages

## Skills Demonstrated

- IEC 61131-3 Ladder Diagram programming
- Seal-in (latching) motor control
- Using `TON` timers and `CTU` counters
- CODESYS project setup, task configuration and simulation
- HMI design with CODESYS Visualization
- Testing and debugging PLC logic online


