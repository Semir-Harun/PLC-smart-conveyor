# 13 - First Run Checklist (15 Minutes)

Use this during your first live execution in TIA V17 + PLCSIM + Factory I/O.

## 0:00-2:00 - Open and prepare

- [ ] Open TIA Portal V17
- [ ] Open `PLC_1200_SmartConveyor.ap17` (or your current S7-1200 project)
- [ ] Confirm no compile errors
- [ ] Open watch table with key tags:
  - `DI_EStopOk`, `DI_GuardClosed`, `DI_OverloadOk`, `DI_AirPressureOk`
  - `Cmd_Start`, `Cmd_Stop`, `Cmd_Reset`
  - `DO_MotorRunCmd`
  - `DB_FIFO`.Count
  - `DB_Alarms`.Alm_JamDetected

## 2:00-5:00 - Start PLCSIM and load

- [ ] Start S7-PLCSIM V17
- [ ] Download PLC program to simulated CPU
- [ ] Set CPU to RUN
- [ ] Verify all safety permissive inputs are TRUE (force if needed)

Pass criteria:
- [ ] `SafeOk` is TRUE
- [ ] `St_Ready` is TRUE

## 5:00-8:00 - Core PLC smoke test (without Factory I/O)

- [ ] Toggle `Cmd_Start` (or `DI_StartPB`) -> confirm `DO_MotorRunCmd=1`
- [ ] Pulse `DI_PE_Infeed` three times -> confirm `DB_FIFO`.Count increments
- [ ] Pulse divert sensor (`DI_PE_DivA` / pop trigger) -> confirm Count decrements
- [ ] Trigger jam case (infeed without pop) -> confirm `Alm_JamDetected`
- [ ] Reset sequence -> confirm alarm clears and run can resume

Pass criteria:
- [ ] FIFO push/pop works
- [ ] Jam fault latches and resets correctly

## 8:00-12:00 - Connect Factory I/O

- [ ] Open Factory I/O and load sorting/diverter scene
- [ ] Select driver: Siemens `S7-PLCSIM`
- [ ] Map minimum signals:
  - Infeed sensor -> `I0.5` (`DI_PE_Infeed`)
  - Divert sensor -> `I0.6` (`DI_PE_DivA`) or chosen pop sensor
  - Motor run -> `Q0.0` (`DO_MotorRunCmd`)
  - Diverter A -> `Q0.1`
  - Diverter B -> `Q0.2`
  - Reject -> `Q0.3`

Pass criteria:
- [ ] Scene starts/stops from PLC command
- [ ] At least one sorted item reaches expected lane

## 12:00-15:00 - Capture evidence

- [ ] Record a 20-30 second clip (normal sort + one fault + reset)
- [ ] Take 2 screenshots:
  - Watch table with FIFO/alarm activity
  - Factory I/O scene while sorting
- [ ] Fill baseline values in `docs/12-measured-results-template.md`

Save evidence to:

- `media/demo-video/`
- `media/screenshots/`

---

## If something fails (quick triage)

1. Verify driver connection (PLCSIM selected, correct adapter/settings).
2. Verify symbolic tag/address mapping matches PLC project.
3. Verify safety permissives are TRUE; otherwise run command will be blocked.
4. If FIFO does not pop, verify correct sensor is used as pop trigger.
