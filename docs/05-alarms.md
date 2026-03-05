# Alarm Table (Warnings & Faults)

## Philosophy
- **Faults** (F) stop the system and are latched until reset conditions are satisfied.
- **Warnings** (W) do not necessarily stop the system, but notify the operator.
- All alarms are visible on HMI with clear operator actions.

Recommended HMI behavior:
- Active alarms list + acknowledge
- Fault banner on top of all screens when any F is active
- Red stacklight for faults; Yellow for warnings; Green when running

---

## Alarm Table

| ID | Name (Tag) | Severity | Trigger Condition (Example) | System Response | Operator Action / Reset |
|---:|---|:---:|---|---|---|
| F001 | `Alm_EStop` | F | `NOT DI_EStopOk` | Immediate safe stop; latch fault | Restore E-stop chain → press Reset |
| F002 | `Alm_GuardOpen` | F | `NOT DI_GuardClosed` | Safe stop; latch fault | Close guard → press Reset |
| F003 | `Alm_Overload` | F | `NOT DI_OverloadOk` | Stop motor; latch fault | Clear overload at drive/motor → Reset |
| F004 | `Alm_AirPressureLow` | F | `NOT DI_AirPressureOk` | Stop; inhibit solenoids; latch fault | Restore air pressure → Reset |
| F005 | `Alm_JamDetected` | F | Product detected but no downstream confirmation within `T_Jam` | Stop; latch fault | Clear jam → Reset → Start |
| F006 | `Alm_FIFO_Overflow` | F | FIFO Count >= Max and new infeed arrives | Stop; latch fault | Investigate spacing/sensors → Reset |
| F007 | `Alm_FIFO_Underflow` | F | Pop requested while Count = 0 | Stop; latch fault | Check sensor mapping / logic → Reset |
| F008 | `Alm_DiverterTimeout_A` | F | Divert A commanded but `DI_PE_DivA` / confirmation not seen within `T_DivConfirm` | Stop; latch fault | Inspect diverter A / sensor → Reset |
| F009 | `Alm_DiverterTimeout_B` | F | Divert B commanded but confirmation not seen within `T_DivConfirm` | Stop; latch fault | Inspect diverter B / sensor → Reset |
| F010 | `Alm_VFD_Fault` | F | VFD fault input active / comm error | Stop; latch fault | Clear VFD fault → Reset |
| F011 | `Alm_SafetyMismatch` | F | Safety signals inconsistent (e.g., guard open but safety ok) | Stop; latch fault | Inspect wiring/safety relay → Reset |

| W101 | `Wrn_Barcode_NoRead` | W | Barcode enabled, product seen, `BarcodeOk=FALSE` | Route to reject (configurable) | Clean scanner / adjust position |
| W102 | `Wrn_Color_Unknown` | W | Color enabled, invalid/unknown `ColorId` | Route to reject (configurable) | Check sensor teach / lighting |
| W103 | `Wrn_LoadCell_Invalid` | W | Weight enabled, value out of range or unstable | Route to reject (configurable) | Check load cell wiring/tare |
| W104 | `Wrn_Product_SpacingLow` | W | Infeed triggers too fast (< `T_MinSpacing`) | May reject or pause (configurable) | Increase spacing / slow feed |
| W105 | `Wrn_DiverterCycleHigh_A` | W | A cycles exceed maintenance threshold | No stop | Perform maintenance |
| W106 | `Wrn_DiverterCycleHigh_B` | W | B cycles exceed maintenance threshold | No stop | Perform maintenance |
| W107 | `Wrn_AirUsageHigh` | W | Many actuations in short time window | No stop | Inspect leaks / adjust timing |
| W108 | `Wrn_SensorChatter_Infeed` | W | Infeed photoeye toggles rapidly | No stop (or pause if severe) | Realign sensor / debounce |
| W109 | `Wrn_CommLatency_Scanner` | W | Scanner response exceeds `T_ScanMax` | Optional reject | Check network/device |

---

## Reset Rules (Recommended)
A fault can be reset only when:
- Safety is OK: `DI_EStopOk AND DI_GuardClosed AND DI_OverloadOk AND DI_AirPressureOk`
- Motor command is off
- Operator presses **Reset**
- Optional: a 1–2 second “stable safety” timer has elapsed

Warnings clear automatically when the condition disappears, but can remain visible in history.
# 05 - Alarms

## Document Scope
- This document is the canonical alarm taxonomy and HMI alarm behavior reference.
- Safety boundary and interlocks are defined in `docs/04-faults-and-safety.md`.
- Fault-state transitions are defined in `docs/04-state-machine.md`.

## Alarm Strategy
- Critical alarms stop motion and latch until reset conditions are met.
- Process alarms warn operator but may allow controlled continuation depending on policy.
- Alarms are represented in `DB_Alarms` and mirrored to HMI.

## Critical Alarms
- `Alm_EStop`
- `Alm_GuardOpen`
- `Alm_Overload`
- `Alm_AirLow`
- `Alm_JamDetected`

## Process Alarms
- `Alm_BarcodeNoRead`
- `Alm_ColorUnknown`
- `Alm_LoadCellInvalid`
- `Alm_FIFO_Overflow`

## HMI Behavior
- Alarm banner visible when any active alarm exists
- Acknowledge action available from alarms page
- Red stacklight and buzzer pattern for critical alarms
- Yellow stacklight for process warnings

## Reset Policy
Reset allowed only when:
- alarm root cause is cleared,
- operator has acknowledged required alarms,
- safety/permissives are healthy.
