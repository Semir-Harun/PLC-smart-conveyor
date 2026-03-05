# 06 - Factory I/O Mapping

## Scene Scope (portable)
- 1 infeed sensor
- 2 divert position sensors
- 3 solenoids (Lane A, Lane B, Reject)
- 1 motor run command
- optional speed reference

## Mapping for S7-1200 project
### Inputs
- `I0.5` -> `DI_PE_Infeed`
- `I0.6` -> `DI_PE_DivA`
- `I0.7` -> `DI_PE_DivB`

### Outputs
- `Q0.0` -> `DO_MotorRunCmd`
- `Q0.1` -> `DO_DivA_Sol`
- `Q0.2` -> `DO_DivB_Sol`
- `Q0.3` -> `DO_Reject_Sol`

### Optional Analog
- `IW64` -> `AI_LoadCell_mA` (raw)
- `QW64` -> `AO_VFD_SpeedRef`

## Mapping for S7-1500 project
### Inputs
- `I0.5` -> `DI_PE_Infeed`
- `I0.6` -> `DI_PE_DivA`
- `I0.7` -> `DI_PE_DivB`

### Outputs
- `Q0.0` -> `DO_MotorRunCmd`
- `Q0.1` -> `DO_DivA_Sol`
- `Q0.2` -> `DO_DivB_Sol`
- `Q0.3` -> `DO_Reject_Sol`

### Optional Analog
- `IW64` -> `AI_LoadCell_mA` (raw)
- `QW64` -> `AO_VFD_SpeedRef`

## Notes
- Prefer symbolic tags in logic and HMI; keep absolute addresses in commissioning docs.
- If hardware layout differs, retain symbolic names and update only hardware mapping.
