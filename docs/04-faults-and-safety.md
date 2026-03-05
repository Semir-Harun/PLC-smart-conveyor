# 04 - Faults and Safety

## Document Scope
- This document is the canonical reference for safety boundary, interlocks, and fault response.
- Alarm taxonomy and HMI alarm behavior are detailed in `docs/05-alarms.md`.
- State transitions during faults are defined in `docs/04-state-machine.md`.

## Safety Boundary
Safety functions are implemented in dedicated safety hardware (safety relay or safety PLC). The standard PLC monitors resulting permissives and status only.

## Mandatory Interlocks
- E-stop healthy
- Guards closed
- Air pressure healthy
- Drive ready/no fault

## Fault List (Example)
| Code | Alarm Tag | Condition | Response |
|---|---|---|---|
| 1001 | ALM_Safety_EstopActive | E-stop loop not healthy | Immediate stop, latch |
| 1002 | ALM_Safety_GuardOpen | Guard opened during operation | Immediate stop, latch |
| 2001 | ALM_Conveyor_StartTimeout | Run cmd active but no speed fb in time | Stop, latch |
| 2002 | ALM_Sort_Jam | Sort PE blocked longer than threshold | Stop, latch |
| 2003 | ALM_Pneumatic_LowPressure | Pressure switch false in auto | Stop, latch |
| 2004 | ALM_Actuator_ReturnTimeout | Diverter not returned in time | Stop, latch |

## Alarm Set for DB_Alarms (summary)
### Critical
- Alm_EStop
- Alm_GuardOpen
- Alm_Overload
- Alm_AirLow
- Alm_JamDetected

### Process
- Alm_BarcodeNoRead
- Alm_ColorUnknown
- Alm_LoadCellInvalid
- Alm_FIFO_Overflow

## Fault Handling Rules
1. Detect and classify fault.
2. Force outputs to safe state.
3. Latch code and timestamp.
4. Present alarm text + help on HMI.
5. Allow reset only when fault cause cleared.

## Reset Conditions
- Fault source removed.
- Operator acknowledges alarm.
- Reset pushbutton or HMI reset command true.
- Safety chain healthy.

## HMI Alarm UX Minimum
- Active alarm list (latest first)
- Alarm history log
- Fault code + plain language message
- Recovery instruction text
- Alarm banner with acknowledge action
- Red stacklight + buzzer pattern for critical faults
- Yellow stacklight for process warnings

## Validation Tests
- Trigger each fault intentionally during FAT.
- Verify expected stop behavior and alarm message.
- Verify reset blocked while condition persists.
