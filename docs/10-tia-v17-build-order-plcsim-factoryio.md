# 10 - Step-by-Step Build Order (TIA V17 -> PLCSIM -> Factory I/O)

This runbook is the execution order for building a dual-target Siemens implementation and validating it in simulation.

Fast execution companion:
- `docs/13-first-run-15min-checklist.md`

## 1) Create two TIA projects (same code structure)

Create two separate projects:

- `PLC_1200_SmartConveyor.ap17` (CPU: S7-1214C DC/DC/DC)
- `PLC_1500_SmartConveyor.ap17` (CPU: S7-1511-1 PN or S7-1513-1 PN)

In both projects:

- Add one PLC device
- Add one HMI device (optional at this stage)

Goal: keep block names and DB names identical in both projects.

---

## 2) Create global tag tables (clean + reviewable)

Create these tag tables in each project.

### Tag table: `IO_DI`

- `DI_EStopOk` (BOOL)
- `DI_GuardClosed`
- `DI_StartPB`
- `DI_StopPB`
- `DI_ResetPB`
- `DI_PE_Infeed`
- `DI_PE_DivA`
- `DI_PE_DivB`
- `DI_OverloadOk`
- `DI_AirPressureOk`

### Tag table: `IO_DO`

- `DO_MotorRunCmd`
- `DO_DivA_Sol`
- `DO_DivB_Sol`
- `DO_Reject_Sol`
- `DO_Stack_Green`
- `DO_Stack_Yellow`
- `DO_Stack_Red`
- `DO_Buzzer`

### Tag table: `ANALOG`

- `AI_LoadCell_Raw` (INT or WORD)
- `LoadCell_kg` (REAL)
- `AO_VFD_SpeedRef` (INT/REAL depending on simulation approach)

Addressing example:

- Digital inputs: `I0.0 ...`
- Digital outputs: `Q0.0 ...`
- Raw analog input: e.g., `IW64`

If Factory I/O analog is unavailable in the selected scene/driver combination, simulate analog via DB/HMI.

---

## 3) Create UDT + DBs (professional backbone)

### UDT: `UDT_Product`

- `Id : DINT`
- `ColorId : INT`
- `WeightKg : REAL`
- `BarcodeOk : BOOL`
- `BarcodeHash : DINT`
- `Decision : INT`

### DB: `DB_Params`

- `Enable_Barcode : BOOL`
- `Enable_Color : BOOL`
- `Enable_Weight : BOOL`
- `Weight_A_Max : REAL`
- `Weight_B_Max : REAL`
- `DiverterPulseTime : TIME` (e.g., `T#150MS`)
- `RejectPulseTime : TIME` (e.g., `T#200MS`)
- `JamTimeout : TIME` (e.g., `T#3S`)
- `MinSpacing : TIME` (e.g., `T#250MS`)
- `ColorToLane : ARRAY[0..10] OF INT`
- `BarcodeHashList : ARRAY[0..19] OF DINT`
- `BarcodeLaneList : ARRAY[0..19] OF INT`

### DB: `DB_FIFO`

- `Buf : ARRAY[0..(N-1)] OF UDT_Product`
- `Head : INT`
- `Tail : INT`
- `Count : INT`
- `NextId : DINT`

Sizing:

- S7-1200: `N = 50`
- S7-1500: `N = 200`

### DB: `DB_Alarms`

- Alarm bits such as `Alm_EStop`, `Alm_GuardOpen`, `Alm_JamDetected`, etc.

### DB: `DB_Counters`

- `Total : DINT`
- `LaneA : DINT`
- `LaneB : DINT`
- `Reject : DINT`
- `JamCount : DINT`

### DB: `DB_HMI`

- Commands: `Cmd_Start`, `Cmd_Stop`, `Cmd_Reset`, `Cmd_Auto`, `Cmd_Manual`
- Status: `St_Ready`, `St_Running`, `St_Fault`
- Setpoints: `SpdPct`, `ModeSelect` (optional)

---

## 4) Build PLC blocks (minimal runnable set)

Create these blocks:

- `FC_ComputeDecision` (SCL)
  - Uses `DB_Params` and inputs (ColorId/Weight/Barcode)
  - Returns Decision (`1=A`, `2=B`, `3=Reject`)

- `FB_SafetyInterlocks` (LAD or SCL)
  - Outputs `SafeOk`, `FaultLatched`
  - Sets alarms (`Alm_EStop`, `Alm_GuardOpen`, ...)

- `FB_ModeManager`
  - Auto/Manual logic
  - Start/Stop/Reset logic
  - Produces `RunningEnable`

- `FB_ProductTrackingFIFO` (SCL)
  - On rising edge of `DI_PE_Infeed`: push product record + decision
  - Enforce `MinSpacing` timer (warning)

- `FB_DiverterActuation` (SCL or LAD)
  - On rising edge of divert position sensor: pop FIFO
  - Pulse solenoids with `TP` timers
  - Update counters

- `FB_JamDetection`
  - If product is detected but no divert occurs within `JamTimeout`: fault

Wire OB1 in this order:

1. Safety
2. Mode/Commands
3. Conveyor run output
4. FIFO push
5. Diverter pop + actuation
6. Jam logic
7. Alarms/Counters/HMI status

Add `OB100` to initialize `DB_Params` defaults and reset FIFO pointers.

---

## 5) PLCSIM test (before Factory I/O)

Run PLCSIM and execute watch table tests:

- Force `DI_EStopOk=1`, `DI_GuardClosed=1`, `DI_OverloadOk=1`, `DI_AirPressureOk=1`
- Toggle `Cmd_Start` (or `DI_StartPB`) and confirm `DO_MotorRunCmd=1`
- Pulse `DI_PE_Infeed` multiple times and confirm FIFO `Count` increases
- Pulse divert sensor and confirm FIFO `Count` decreases + one solenoid pulse
- Trigger jam case (infeed without divert) and confirm `Alm_JamDetected` + motor stop

This validates core logic before external simulation mapping.

---

## 6) Factory I/O setup (Siemens driver)

In Factory I/O:

- Load a conveyor sorting/diverter scene
- Select driver: Siemens `S7-PLCSIM`
- Map tags:
  - Infeed sensor -> `I0.5` (`DI_PE_Infeed`)
  - Divert sensor -> `I0.6` (`DI_PE_DivA`) or one position sensor used as pop trigger
  - Motor run -> `Q0.0` (`DO_MotorRunCmd`)
  - Diverter A -> `Q0.1`
  - Diverter B -> `Q0.2`
  - Reject -> `Q0.3`

Run the scene and confirm sorting.

Portfolio-friendly scope:

- 2 lanes + reject
- One active decision mode (Color or Weight)
- Demonstrate mode switching later on a Config page

---

## 7) HMI (WinCC) after core logic works

Detailed HMI execution checklist:
- `docs/11-wincc-build-checklist.md`

Minimum pages:

- Overview (Start/Stop, Auto/Manual, `SafeOk`, Fault, counters)
- Config (mode toggles + thresholds)
- Alarms (active list + reset guidance)
- Manual (jog solenoids gated by `SafeOk`)

Bind tags to:

- `DB_HMI`
- `DB_Params`
- `DB_Alarms`
- `DB_Counters`

---

## 8) Export artifacts for GitHub (must-do)

From both TIA V17 projects export:

- Tag tables -> `tag_list.csv`
- Block prints -> `ladder_logic.pdf`
- DB layouts (screenshot/PDF)
- HMI screenshots

Also add:

- 30-60 second demo video: Factory I/O run + one fault + reset

---

## Exact next 3 actions

1. Build S7-1200 project first (DBs + minimal FB set)
2. Validate in PLCSIM via watch tables
3. Connect Factory I/O mapping and record first demo clip

Then clone the same logic into the S7-1500 project and only change FIFO size plus optional diagnostics.
