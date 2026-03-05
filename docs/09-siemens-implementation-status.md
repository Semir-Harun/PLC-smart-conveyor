# 09 - Siemens Implementation Status (Finish Line)

This checklist tracks what is complete in documentation/scaffolding versus what must be built and exported from TIA Portal.

Detailed execution sequence is documented in:
- `docs/10-tia-v17-build-order-plcsim-factoryio.md`

## Current State
- Documentation backbone: COMPLETE
- Dual-target folder structure (S7-1200 + S7-1500): COMPLETE
- TIA implementation evidence (real exports / screenshots / demo): PENDING

---

## A) TIA Implementation Artifacts
### S7-1200
- [ ] Build/compile actual PLC project in TIA (`PLC_1200.apXX`)
- [ ] Build/compile actual HMI project in TIA (`HMI_1200.apXX`)
- [ ] Export real `ladder_logic.pdf`
- [ ] Export real `tag_list.csv`
- [ ] Export real `io_list.xlsx`
- [ ] Export real `hmi_screens.pdf`

### S7-1500
- [ ] Build/compile actual PLC project in TIA (`PLC_1500.apXX`)
- [ ] Build/compile actual HMI project in TIA (`HMI_1500.apXX`)
- [ ] Export real `ladder_logic.pdf`
- [ ] Export real `tag_list.csv`
- [ ] Export real `io_list.xlsx`
- [ ] Export real `hmi_screens.pdf`

---

## B) Data Model DBs (same names both CPUs)
- [ ] `DB_Params` implemented (speed, timers, thresholds, mapping tables)
- [ ] `DB_FIFO` implemented (buffer, head/tail/count/nextId)
- [ ] `DB_Alarms` implemented (bits, ack, reset/latch handling)
- [ ] `DB_Counters` implemented (total/lane/reject/uptime/cycles)
- [ ] `DB_HMI` implemented (commands, setpoints, status)

---

## C) PLC Logic Blocks (minimum runnable set)
- [ ] `FB_SafetyInterlocks`
- [ ] `FB_ModeManager`
- [ ] `FB_ConveyorControl`
- [ ] `FB_ProductTrackingFIFO`
- [ ] `FB_DiverterActuation`
- [ ] `FC_ComputeDecision`
- [ ] `FB_AlarmsAndCounters`
- [ ] `OB1_Main` wired to all runtime blocks
- [ ] `OB100_Startup` initializes FIFO/counters/params

---

## D) Factory I/O / PLCSIM Integration
- [ ] Factory I/O scene selected and configured
- [ ] Driver setup verified for S7-1200 target
- [ ] Driver setup verified for S7-1500 target
- [ ] Address mapping validated against `docs/06-factory-io-mapping.md`
- [ ] Sorting + alarm behavior validated in simulation

---

## E) WinCC HMI (recommended minimum)
- [ ] Overview screen (Start/Stop/Mode/Status/Counters)
- [ ] Config screen (thresholds/mapping)
- [ ] Manual screen (jog outputs, safety-gated)
- [ ] Alarms screen (active list + acknowledge)
- [ ] Maintenance screen (cycles + sensor health)

---

## F) Portfolio Evidence Pack
- [ ] 30–60 sec demo video (sorting + jam alarm + reset)
- [ ] Screenshots: Overview / Alarms / Trends / Config / Manual
- [ ] FIFO concept screenshot (watch table or DB view)
- [ ] Measured KPI report completed (`docs/12-measured-results-template.md`)
- [ ] README links updated to real exports and media

---

## Practical Note
The remaining items above must be executed in Siemens TIA Portal / PLCSIM / Factory I/O and then committed into this repository. This workspace can prepare structure and documentation but cannot generate native TIA runtime exports directly.
