# Smart Conveyor Sorting System

Production-ready PLC project package for a smart conveyor sorting cell with sensor-based classification, diverter actuation, HMI operation, alarm handling, and commissioning support.

---

## 1) Project Overview

This repository contains documentation, PLC project placeholders, exports, and visual diagrams for **Project 1: Smart Conveyor Sorting System**.

### Goals
- Sort incoming items to correct lanes based on sensor or recipe criteria.
- Provide robust machine control with clear operating states.
- Ensure fault-safe behavior and straightforward commissioning.
- Keep structure compatible across Siemens, Rockwell, and CODESYS toolchains.

### Typical Use Cases
- Training and portfolio demonstration
- Factory I/O simulation projects
- Small packaging / logistics sorting cells
- Internal standards template for new PLC projects

## Quick Review (5 Minutes)
1. Open `docs/00-dual-target-notes-1200-vs-1500.md` to see S7-1200 vs S7-1500 strategy.
2. Open `docs/03-software-architecture.md` and `docs/04-state-machine.md` for program structure and control behavior.
3. Open `docs/05-alarms.md` and `docs/04-faults-and-safety.md` for alarm/fault philosophy.
4. Review per-target Siemens exports:
  - `project-files/Siemens-TIA/S7-1200/` (`ladder_logic.pdf`, `tag_list.csv`, `io_list.xlsx`, `hmi_screens.pdf`)
  - `project-files/Siemens-TIA/S7-1500/` (`ladder_logic.pdf`, `tag_list.csv`, `io_list.xlsx`, `hmi_screens.pdf`)
5. Check `docs/06-factory-io-mapping.md` for deployment mapping in simulation.
6. Capture and report objective KPI evidence using `docs/12-measured-results-template.md`.
7. Use `docs/13-first-run-15min-checklist.md` for the first live execution pass.

---

## 2) Repository Structure

```text
plc-smart-conveyor-sorting/
├─ README.md
├─ project-files/
│  ├─ Siemens-TIA/
│  │  ├─ S7-1200/
│  │  │  ├─ PLC_1200.apXX
│  │  │  ├─ HMI_1200.apXX
│  │  │  ├─ ladder_logic.pdf
│  │  │  ├─ tag_list.csv
│  │  │  ├─ io_list.xlsx
│  │  │  └─ hmi_screens.pdf
│  │  ├─ S7-1500/
│  │  │  ├─ PLC_1500.apXX
│  │  │  ├─ HMI_1500.apXX
│  │  │  ├─ ladder_logic.pdf
│  │  │  ├─ tag_list.csv
│  │  │  ├─ io_list.xlsx
│  │  │  └─ hmi_screens.pdf
│  │  ├─ TIA_Industrial_Template.md
│  │  ├─ ConveyorSort_SCL_Skeleton.scl
│  │  ├─ PLC_Project_TIA.apXX
│  │  └─ HMI_Project_TIA.apXX
│  ├─ Rockwell-RSLogix/
│  │  └─ ConveyorSort.ACD
│  ├─ CODESYS/
│  │  ├─ ConveyorSort.project
│  │  └─ ConveyorSort_ST.txt
│  └─ exports/
│     ├─ ladder_logic.pdf
│     ├─ tag_list.csv
│     ├─ io_list.xlsx
│     └─ hmi_screens.pdf
├─ docs/
│  ├─ 00-dual-target-notes-1200-vs-1500.md
│  ├─ 01-architecture.md
│  ├─ 02-io-mapping.md
│  ├─ 03-software-architecture.md
│  ├─ 04-state-machine.md
│  ├─ 05-alarms.md
│  ├─ 06-factory-io-mapping.md
│  ├─ 03-sequence-and-state-machine.md
│  ├─ 04-faults-and-safety.md
│  ├─ 05-commissioning-checklist.md
│  ├─ 06-fat-results-template.md
│  ├─ 07-sat-results-template.md
│  ├─ 08-project-handover-summary.md
│  ├─ 09-siemens-implementation-status.md
│  └─ diagrams/
│     ├─ architecture.mmd
│     ├─ state-machine.mmd
│     └─ sequence.mmd
└─ media/
   ├─ screenshots/
   │  ├─ hmi_overview.png
   │  ├─ alarms.png
   │  └─ trends.png
   ├─ demo-video/
   │  └─ factoryio_demo.mp4
   └─ gifs/
      └─ sorting_loop.gif
```

---

## 3) Control Architecture

## Siemens TIA Compatibility (S7-1200 + S7-1500)
This repository includes two Siemens TIA Portal implementations:
- `project-files/Siemens-TIA/S7-1200/` (compact machine-style deployment)
- `project-files/Siemens-TIA/S7-1500/` (higher performance line-style deployment)

The control architecture (FB/FC/DB naming, state machine, FIFO tracking, alarms, HMI pages) is identical across both.
Differences are documented in `docs/00-dual-target-notes-1200-vs-1500.md` (FIFO sizing, diagnostics depth, performance headroom).

### CPU Assumptions
- S7-1200 option: CPU S7-1214C (or 1212C), HMI KTP700 Basic, VFD G120 or similar
- S7-1500 option: CPU S7-1511/1513, HMI Comfort Panel (TP700/TP900), VFD G120/SINAMICS with extended diagnostics

### Siemens Proof (Implementation Status)
- Dual-target architecture/documentation: complete
- Per-CPU artifact folders: present (currently placeholders until exported from TIA)
- Real proof pending from TIA/PLCSIM/Factory I/O: final PLC/HMI builds, exports, screenshots, demo clip
- Tracking checklist: `docs/09-siemens-implementation-status.md`

### Core Subsystems
- **Infeed Conveyor Control**: motor run, speed reference, permissives.
- **Detection Zone**: photoeye + optional analog/vision/classification input.
- **Tracking / Timing**: delay compensation from detection to diverter point.
- **Sorting Actuator**: pusher, flap, or diverter gate.
- **Reject Path**: fallback route for unknown or faulty classification.
- **HMI / SCADA Layer**: mode commands, diagnostics, counters, alarms.

### Recommended PLC Program Blocks
- `MainCycle` (supervisory state machine)
- `ModeManager` (Auto/Manual/Service)
- `ConveyorCtrl`
- `SortDecision`
- `PartTracker`
- `ActuatorCtrl`
- `AlarmManager`
- `SafetyInterlocks`

---

## 4) Example I/O Mapping (Digital + Analog)

> Adapt addressing to your PLC platform and hardware rack.

### Digital Inputs (DI)
| Tag | Address | Description |
|---|---|---|
| `DI_ESTOP_OK` | `%I0.0` | E-stop chain healthy (safety relay feedback) |
| `DI_GUARD_CLOSED` | `%I0.1` | Guard doors closed |
| `DI_START_PB` | `%I0.2` | Start pushbutton |
| `DI_STOP_PB` | `%I0.3` | Stop pushbutton |
| `DI_RESET_PB` | `%I0.4` | Alarm reset pushbutton |
| `DI_PE_INFEED` | `%I0.5` | Item detect at infeed |
| `DI_PE_SORT_POS` | `%I0.6` | Item at sort point |
| `DI_AIR_PRESS_OK` | `%I0.7` | Pneumatic pressure switch |

### Digital Outputs (DO)
| Tag | Address | Description |
|---|---|---|
| `DO_CONV_RUN` | `%Q0.0` | Conveyor motor run command |
| `DO_DIVERTER_EXTEND` | `%Q0.1` | Diverter/pusher actuate |
| `DO_STACKLIGHT_GREEN` | `%Q0.2` | Machine run indication |
| `DO_STACKLIGHT_AMBER` | `%Q0.3` | Warning indication |
| `DO_STACKLIGHT_RED` | `%Q0.4` | Fault indication |
| `DO_BUZZER` | `%Q0.5` | Audible alarm |

### Analog Inputs (AI)
| Tag | Address | Scale | Description |
|---|---|---|---|
| `AI_WEIGHT_RAW` | `%IW64` | 0..27648 | Item weight input (load cell transmitter) |
| `AI_SPEED_FB_RAW` | `%IW66` | 0..27648 | Conveyor speed feedback |

### Analog Outputs (AO)
| Tag | Address | Scale | Description |
|---|---|---|---|
| `AO_SPEED_REF_RAW` | `%QW64` | 0..27648 | Speed reference to VFD |

---

## 5) Logic Flow and State Machine

### Primary Sequence
1. **Idle**: waiting for Start and all permissives true.
2. **Run**: conveyor running, detect items, classify each item.
3. **Track**: maintain part context until sort position.
4. **Actuate**: trigger diverter for configurable pulse time.
5. **Confirm**: optional sensor check for successful sort.
6. **Count**: increment Good/Reject counters.
7. **Loop**: continue until Stop or fault.

### Suggested States
- `S0_STOPPED`
- `S1_READY`
- `S2_AUTO_RUN`
- `S3_SORT_ACTUATE`
- `S4_FAULT`
- `S5_MANUAL`

Transitions and details are provided in `docs/03-sequence-and-state-machine.md` and Mermaid diagrams in `docs/diagrams`.

---

## 6) Fault Handling and Safety Concept

### Safety Principles
- Safety circuit hardwired through safety relay / safety PLC.
- Standard PLC only reads safety feedback status (never replaces safety function).
- Motion outputs drop immediately when safety permissive is lost.

### Core Faults
- E-stop active / guard open
- Conveyor start timeout (run command but no speed feedback)
- Jam detected at sort point (photoeye blocked too long)
- Air pressure low for pneumatic diverter
- Diverter failed to return (if return sensor present)

### Fault Strategy
- Latch fault with timestamp and code.
- Stop conveyor and de-energize actuators to safe state.
- Show clear HMI alarm text and reset conditions.
- Require operator acknowledgement + reset permissives.

---

## 7) Commissioning Checklist (Summary)

- Verify electrical IO point-to-point and tag names.
- Validate safety chain and stop categories.
- Confirm motor direction and VFD scaling.
- Tune tracking delay from infeed PE to diverter point.
- Test every alarm, interlock, and reset path.
- Run mixed product test and verify sorting accuracy.
- Archive final PLC/HMI backups and export tag/IO lists.

Full checklist: `docs/05-commissioning-checklist.md`.

---

## 8) Naming Standard

- Prefixes: `DI_`, `DO_`, `AI_`, `AO_`, `M_`, `T_`, `C_`, `ALM_`.
- Commands: `CMD_` (e.g., `CMD_StartAuto`).
- Status: `STS_` (e.g., `STS_AutoRunning`).
- Alarms: `ALM_<Area>_<Condition>`.

Example:
- `ALM_Conveyor_StartTimeout`
- `ALM_Sort_Jam`
- `STS_SafetyPermissive`

Siemens-ready naming and block structure package is included in:
- `project-files/Siemens-TIA/TIA_Industrial_Template.md`
- `project-files/Siemens-TIA/ConveyorSort_SCL_Skeleton.scl`

## 8.1) WinCC Minimum Screens
- Overview: Start/Stop/Reset, Auto/Manual, status lamps, speed setpoint, counters
- Sorting Config: mode enables, weight thresholds, color map, barcode hash-to-lane map
- Manual: jog motor, pulse diverters, I/O status panel
- Alarms: active alarms, optional history, ack/reset guidance
- Maintenance: sensor health and actuator cycle counts

---

## 9) How to Use This Package

1. Place actual PLC/HMI project files into `project-files/` platform folders.
2. Export ladder/ST printouts, tag lists, IO list, and HMI pages to `project-files/exports/`.
3. Keep architecture and sequence docs updated after each revision.
4. Add screenshots and demo media under `media/`.
5. Use pull requests for logic changes impacting states, safety, or alarms.

### Canonical Documentation Map
- Dual-target Siemens differences: `docs/00-dual-target-notes-1200-vs-1500.md`
- Architecture overview: `docs/01-architecture.md`
- IO mapping and Siemens symbolic tags: `docs/02-io-mapping.md`
- Software architecture (OB/FB/FC/DB): `docs/03-software-architecture.md`
- State machine: `docs/04-state-machine.md`
- Fault and safety boundary: `docs/04-faults-and-safety.md`
- Alarm model and reset policy: `docs/05-alarms.md`
- Factory I/O deployment mapping: `docs/06-factory-io-mapping.md`
- Siemens build finish-line tracker: `docs/09-siemens-implementation-status.md`
- Performance/quality KPI evidence template: `docs/12-measured-results-template.md`

### Siemens Dual-Target Review Package
For recruiter/company review without opening full `.apXX` files, provide per-CPU exports in:
- `project-files/Siemens-TIA/S7-1200/` -> `ladder_logic.pdf`, `tag_list.csv`, `io_list.xlsx`, `hmi_screens.pdf`
- `project-files/Siemens-TIA/S7-1500/` -> `ladder_logic.pdf`, `tag_list.csv`, `io_list.xlsx`, `hmi_screens.pdf`

Use export checklists during final TIA handover:
- `project-files/Siemens-TIA/S7-1200/EXPORT_MANIFEST.md`
- `project-files/Siemens-TIA/S7-1500/EXPORT_MANIFEST.md`

---

## 10) Versioning Guidance

- Tag milestones (example): `v1.0-FAT`, `v1.1-SAT`, `v1.2-Production`.
- Record changes in commit messages with area prefix:
  - `logic: adjust sort pulse timing`
  - `safety: add air pressure permissive`
  - `hmi: add alarm help text`

---

## 11) Disclaimer

This repository is a template and documentation package. Final safety design and validation must follow local regulations, machine risk assessment, and company standards.
