# 11 - WinCC Build Checklist (After Core PLC Works)

Use this checklist only after PLC core logic is validated in PLCSIM/Factory I/O.

## 1) Project Setup

- [ ] Add WinCC HMI device to project
- [ ] Set communication to PLC CPU (same project, correct connection)
- [ ] Verify tag update online with one known status bit (e.g., `DB_HMI`.St_Ready)

Deliverable:
- [ ] HMI device compiles without errors

---

## 2) HMI Tag Binding Structure

Create HMI tag groups (names can match DBs):

- [ ] `HMI_Cmd` -> `DB_HMI` commands
- [ ] `HMI_Status` -> `DB_HMI` status bits
- [ ] `HMI_Params` -> `DB_Params` setpoints/toggles
- [ ] `HMI_Alarms` -> `DB_Alarms` bits
- [ ] `HMI_Count` -> `DB_Counters`

Minimum bound tags:

- Commands: `Cmd_Start`, `Cmd_Stop`, `Cmd_Reset`, `Cmd_Auto`, `Cmd_Manual`
- Status: `St_Ready`, `St_Running`, `St_Fault`, `SafeOk`
- Counters: `Total`, `LaneA`, `LaneB`, `Reject`, `JamCount`
- Parameters: `Enable_Barcode`, `Enable_Color`, `Enable_Weight`, `Weight_A_Max`, `Weight_B_Max`

Deliverable:
- [ ] All minimum tags compile and show valid connection status

---

## 3) Screen 1 - Overview

Required objects:

- [ ] Start / Stop / Reset buttons
- [ ] Auto / Manual mode selector
- [ ] Status lamps for Ready / Running / Fault / SafeOk
- [ ] Numeric fields for counters (Total, LaneA, LaneB, Reject)
- [ ] Optional speed setpoint/value (`SpdPct`)

Behavior checks:

- [ ] Start only effective when safety/permissives are true
- [ ] Stop always halts run command
- [ ] Fault indicator reflects PLC state without delay

---

## 4) Screen 2 - Config

Required objects:

- [ ] Toggle inputs for decision modes (Barcode/Color/Weight)
- [ ] Numeric input for `Weight_A_Max`
- [ ] Numeric input for `Weight_B_Max`
- [ ] Optional table/list widgets for color/barcode mapping

Behavior checks:

- [ ] Changing a setpoint updates PLC DB value
- [ ] Invalid ranges are constrained (min/max limits configured)

---

## 5) Screen 3 - Manual

Required objects:

- [ ] Motor jog command
- [ ] Diverter A pulse command
- [ ] Diverter B pulse command
- [ ] Reject pulse command
- [ ] IO status panel (core DI/DO visibility)

Safety gating rules:

- [ ] Manual outputs only active when `SafeOk=1`
- [ ] Manual commands inhibited in Fault unless reset condition met

---

## 6) Screen 4 - Alarms

Required objects:

- [ ] Active alarms view
- [ ] Acknowledge button
- [ ] Reset guidance text area

Minimum alarms visible:

- [ ] `Alm_EStop`
- [ ] `Alm_GuardOpen`
- [ ] `Alm_Overload`
- [ ] `Alm_AirLow`
- [ ] `Alm_JamDetected`

Behavior checks:

- [ ] Alarm appears on trigger
- [ ] Acknowledge works per alarm class
- [ ] Alarm clears only after condition clears + reset logic is satisfied

---

## 7) Optional Screen 5 - Maintenance

- [ ] Actuator cycle counters
- [ ] Sensor health indicators (last trigger/heartbeat)
- [ ] Service/reset counters with permissions

---

## 8) Security and Permissions (Minimum)

- [ ] Operator can run Start/Stop/Reset and view counters
- [ ] Maintenance can access Manual page
- [ ] Engineer can edit Config setpoints/mappings

If user management is not in scope, document this as a known limitation for FAT/SAT.

---

## 9) Validation Sequence (10-minute smoke test)

- [ ] Start from Overview, run conveyor in Auto
- [ ] Trigger one product path and verify counters increment
- [ ] Force one fault (jam or guard open) and verify alarm display
- [ ] Acknowledge + reset and return to running state
- [ ] Switch to Manual and pulse one diverter with safety OK

---

## 10) Export Evidence for Repo

- [ ] Export HMI screenshots (Overview, Config, Manual, Alarms)
- [ ] Export HMI print/PDF if available
- [ ] Save to `project-files/exports/` and `media/screenshots/`
- [ ] Replace placeholders:
  - `project-files/Siemens-TIA/HMI_Project_TIA.apXX`
  - `project-files/Siemens-TIA/S7-1200/HMI_1200.apXX`
  - `project-files/Siemens-TIA/S7-1500/HMI_1500.apXX`

Definition of done:

- [ ] HMI compiles cleanly
- [ ] All four minimum pages functional
- [ ] Alarm workflow demonstrated
- [ ] Evidence exported and committed
