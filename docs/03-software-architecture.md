# 03 - Software Architecture

## Program Organization
### OBs
- `OB1_Main`: cyclic execution and block orchestration
- `OB100_Startup`: startup initialization for FIFO, counters, and runtime defaults

### FB/FC Modules
- `FB_SafetyInterlocks`
- `FB_ModeManager`
- `FB_ConveyorControl`
- `FB_ProductTrackingFIFO`
- `FB_DiverterActuation`
- `FB_AlarmsAndCounters`
- `FC_ComputeDecision`
- `FC_ScaleLoadCell`

### Data Blocks
- `DB_IO` (optional I/O mirror for simulation)
- `DB_HMI`
- `DB_Params`
- `DB_FIFO`
- `DB_Alarms`
- `DB_Counters`

## Execution Sequence (OB1)
1. Evaluate safety/permissives
2. Evaluate mode and operator commands
3. Run conveyor command logic
4. Capture infeed events and push product records into FIFO
5. Trigger diverters based on product decision at position sensors
6. Update counters, alarms, and HMI status

## UDT Product Record
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

## Portability Principle
Block names and interfaces stay identical for S7-1200 and S7-1500.
Only capacity/parameter tuning and hardware binding differ by target CPU profile.
