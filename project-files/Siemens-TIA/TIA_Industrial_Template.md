# Siemens TIA Industrial Template

This file captures the exact Siemens TIA structure and naming package used for company-facing reviews.

## 1) Naming Convention

### Digital Inputs (I)
- `DI_EStopOk`
- `DI_GuardClosed`
- `DI_StartPB`
- `DI_StopPB`
- `DI_ResetPB`
- `DI_PE_Infeed`
- `DI_PE_DivA`
- `DI_PE_DivB`
- `DI_OverloadOk`
- `DI_AirPressureOk`

### Digital Outputs (Q)
- `DO_MotorRunCmd`
- `DO_DivA_Sol`
- `DO_DivB_Sol`
- `DO_Reject_Sol`
- `DO_Stack_Green`
- `DO_Stack_Yellow`
- `DO_Stack_Red`
- `DO_Buzzer`

### Analog / Tech
- `AI_LoadCell_mA` (raw)
- `LoadCell_kg` (scaled)
- `AO_VFD_SpeedRef` (0–10V / %)
- `VFD_SpeedPct`

### Status / Commands (DB)
- `Cmd_Auto`
- `Cmd_Manual`
- `Cmd_Start`
- `Cmd_Stop`
- `Cmd_Reset`
- `St_Running`
- `St_Fault`
- `St_Ready`

## 2) OB / FB / DB Structure (TIA Best Practice)

### `OB1` (Main)
Calls:
- `FB_SafetyInterlocks`
- `FB_ModeManager`
- `FB_ConveyorControl`
- `FB_ProductTrackingFIFO`
- `FB_DiverterActuation`
- `FB_AlarmsAndCounters`
- `FB_HMI_Interface`

### Data Blocks
- `DB_HMI` (all HMI tags)
- `DB_IO` (optional mirror I/O for simulation)
- `DB_Params` (setpoints: speed, timers, thresholds, SKU maps)
- `DB_FIFO` (product queue + head/tail pointers)
- `DB_Alarms` (bits + texts + timestamps)

## 3) Product Record + FIFO DB

### `UDT_Product`
```text
Id : DINT
Detected : BOOL
ColorId : INT
WeightKg : REAL
BarcodeOk : BOOL
BarcodeHash : DINT
Decision : INT
TimeStampMs : DINT
```

### `DB_FIFO`
```text
Buf : ARRAY[0..49] OF UDT_Product
Head : INT
Tail : INT
Count : INT
NextId : DINT
```

## 4) Sorting Rule Set
Priority order (configurable in `DB_Params`):
1. Barcode (if read OK and exists in mapping)
2. Color
3. Weight thresholds
4. Else Reject

### `DB_Params` example fields
- `Enable_Barcode : BOOL`
- `Enable_Color : BOOL`
- `Enable_Weight : BOOL`
- `Weight_A_Max : REAL`
- `Weight_B_Max : REAL`
- `Reject_Above : REAL`
- `ColorToLane : ARRAY[0..10] OF INT`
- `BarcodeHashList : ARRAY[0..19] OF DINT`
- `BarcodeLaneList : ARRAY[0..19] OF INT`

## 5) Structured Text Skeleton
See `ConveyorSort_SCL_Skeleton.scl` in this same folder.

## 6) Safety & Interlocks
In `FB_SafetyInterlocks`:

```text
SafeOk := DI_EStopOk AND DI_GuardClosed AND DI_OverloadOk AND DI_AirPressureOk
FaultStop := NOT SafeOk
MotorRun permissive := SafeOk AND Cmd_Start AND NOT Cmd_Stop AND NOT FaultLatched
```

Controlled restart rule:
- Safety break -> move to `FAULT` (latched)
- Reset requires: safety OK + Reset PB + stop state

## 7) Alarm List
### Critical
- `Alm_EStop`
- `Alm_GuardOpen`
- `Alm_Overload`
- `Alm_AirLow`
- `Alm_JamDetected`

### Process
- `Alm_BarcodeNoRead`
- `Alm_ColorUnknown`
- `Alm_LoadCellInvalid`
- `Alm_FIFO_Overflow`

HMI behavior:
- Show alarm banner + acknowledge
- Faults latch red stacklight + buzzer pattern
- Yellow stacklight for warnings

## 8) HMI Screens (WinCC Minimum)
- Screen 1: Overview
  - Start/Stop/Reset
  - Auto/Manual toggle
  - Running/Ready/Fault indicators
  - Belt speed setpoint
  - Counters: Total, LaneA, LaneB, Reject
- Screen 2: Sorting Config
  - Mode enable toggles (barcode/color/weight)
  - Weight thresholds
  - Color mapping table
  - Barcode mapping list (hash + lane)
- Screen 3: Manual
  - Jog Motor (with safety OK)
  - Pulse Diverter A/B/Reject (with safety OK)
  - I/O status panel
- Screen 4: Alarms
  - Active alarms list
  - History (optional)
  - Ack/Reset guidance
- Screen 5: Maintenance
  - Sensor health (last trigger time)
  - Actuator cycle counts

## 9) TIA Exports for GitHub
- Tag table -> `project-files/exports/tag_list.csv`
- PLC blocks print -> `project-files/exports/ladder_logic.pdf`
- HMI screens print -> `project-files/exports/hmi_screens.pdf`
- IO list -> `project-files/exports/io_list.xlsx`
