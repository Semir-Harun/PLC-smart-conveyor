# IO Mapping

# 02 - IO Mapping

Use symbolic tags in logic/HMI and keep absolute addresses documented for commissioning.

## Siemens Typical Address Pattern (S7-1200 and S7-1500)

### Digital Inputs (I)
| Address | Tag | Description |
|---|---|---|
| I0.0 | DI_EStopOk | E-stop chain healthy |
| I0.1 | DI_GuardClosed | Guard closed feedback |
| I0.2 | DI_StartPB | Start pushbutton |
| I0.3 | DI_StopPB | Stop pushbutton |
| I0.4 | DI_ResetPB | Reset pushbutton |
| I0.5 | DI_PE_Infeed | Infeed photoeye |
| I0.6 | DI_PE_DivA | Divert A position sensor |
| I0.7 | DI_PE_DivB | Divert B position sensor |
| I1.0 | DI_OverloadOk | Motor overload relay healthy |
| I1.1 | DI_AirPressureOk | Pneumatic pressure healthy |

### Digital Outputs (Q)
| Address | Tag | Description |
|---|---|---|
| Q0.0 | DO_MotorRunCmd | Conveyor motor run command |
| Q0.1 | DO_DivA_Sol | Diverter A solenoid |
| Q0.2 | DO_DivB_Sol | Diverter B solenoid |
| Q0.3 | DO_Reject_Sol | Reject solenoid |
| Q0.4 | DO_Stack_Green | Green stacklight |
| Q0.5 | DO_Stack_Yellow | Yellow stacklight |
| Q0.6 | DO_Stack_Red | Red stacklight |
| Q0.7 | DO_Buzzer | Audible alarm |

### Analog / Technology Tags
| Address | Tag | Type | Description |
|---|---|---|---|
| IW64 | AI_LoadCell_mA | INT (raw) | Load cell transmitter input |
| Internal | LoadCell_kg | REAL | Scaled load cell value |
| QW64 | AO_VFD_SpeedRef | INT/REAL (platform policy) | VFD speed reference |
| Internal | VFD_SpeedPct | REAL | Engineering speed percentage |

### Status / Commands in DB Layer
- Cmd_Auto
- Cmd_Manual
- Cmd_Start
- Cmd_Stop
- Cmd_Reset
- St_Running
- St_Fault
- St_Ready

## Scaling Examples
- `LoadCell_kg := (INT_TO_REAL(AI_LoadCell_mA) / 27648.0) * SpanKg`
- `VFD_SpeedPct := (INT_TO_REAL(AI_SpeedFbRaw) / 27648.0) * 100.0`

## Notes
- Addresses above are typical examples; exact hardware modules may shift address ranges.
- Keep symbolic naming identical across S7-1200 and S7-1500 projects.
- Update only hardware mapping and parameter limits per target CPU profile.