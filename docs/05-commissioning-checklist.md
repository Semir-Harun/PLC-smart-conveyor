# Commissioning Checklist (S7-1200 & S7-1500)

## Scope
This checklist validates the conveyor sorting system for both Siemens CPU variants:
- **S7-1200**: compact deployment (smaller FIFO, essential diagnostics)
- **S7-1500**: expanded deployment (larger FIFO, deeper diagnostics)

Unless noted, steps are identical for both.

---

## A) Pre-Power / Mechanical
- [ ] Verify conveyor mechanical installation, guards, and emergency stop devices
- [ ] Verify pneumatic assembly: solenoids wired, cylinders aligned, no binding
- [ ] Verify air supply: regulator set, filter drained, leaks checked
- [ ] Confirm diverter arms return properly (spring/air return as designed)

**Note**
- S7-1500 variant may include extra sensors and station interlocks—verify all connected devices match the IO list.

---

## B) Electrical / Wiring
- [ ] Confirm PLC power supply voltage and grounding
- [ ] Verify DI/DO wiring against `docs/02-io-mapping.md`
- [ ] Verify safety chain wiring (E-stop, guard interlocks) using the safety relay/inputs
- [ ] Verify motor/VFD wiring, overload contact, and enable circuit
- [ ] Verify analog wiring for load cell (shielding, correct polarity, loop power)
- [ ] Verify network cabling for PROFINET (PLC ↔ HMI ↔ devices)

**Note**
- For S7-1200 projects, keep IO and comm configuration minimal and clearly documented.
- For S7-1500 projects, confirm device names/IPs and network topology are consistent.

---

## C) PLC Download & Basic Checks
- [ ] Download PLC program to CPU (correct target: S7-1200 or S7-1500)
- [ ] Verify CPU in RUN, no diagnostics errors
- [ ] Verify HMI connection and tag updates
- [ ] Confirm time/date and cycle time are reasonable

**Note**
- S7-1500 often supports deeper diagnostics—capture a screenshot of diagnostics buffer for portfolio evidence.

---

## D) I/O Dry Test (No Motion)
### Digital Inputs
- [ ] Test `DI_EStopOk` changes when E-stop is pressed/released
- [ ] Test `DI_GuardClosed` changes when guard is opened/closed
- [ ] Test Start/Stop/Reset pushbuttons on HMI and physical panel
- [ ] Test photoeyes (`DI_PE_Infeed`, `DI_PE_DivA`, `DI_PE_DivB`) with a test object
- [ ] Test overload and air pressure signals

### Digital Outputs
- [ ] Test stacklight/buzzer outputs
- [ ] Jog diverters in **Manual mode** (safety OK required)
- [ ] Verify solenoids energize correct valves and direction

### Analog
- [ ] Verify raw load cell value changes with applied weight
- [ ] Verify scaling to `LoadCell_kg` is correct (use 2–3 known weights)
- [ ] Verify VFD speed reference output changes on setpoint change (if used)

---

## E) Safety Validation
- [ ] With motor running, press E-stop → system stops immediately; fault latched
- [ ] Open guard → system stops; fault latched
- [ ] Simulate overload → system stops; fault latched
- [ ] Drop air pressure signal → system stops/inhibits actuators; fault latched
- [ ] Verify restart is blocked until safety OK + Reset

Acceptance:
- [ ] Faults cannot be cleared without safety restored
- [ ] Manual jog is disabled if safety not OK

---

## F) Motion Validation (Conveyor)
- [ ] Start conveyor in Auto, verify smooth operation
- [ ] Stop command performs controlled stop
- [ ] Verify belt speed setpoint effect (if VFD reference is used)
- [ ] Confirm no unexpected actuation while running

---

## G) Sorting Functional Tests
### 1) Color Mode
- [ ] Enable color sorting; set mapping (e.g., Red→A, Blue→B, Unknown→Reject)
- [ ] Send 10 products per color; verify lane counts match

### 2) Weight Mode
- [ ] Configure thresholds (A max, B max)
- [ ] Test 3 weight groups (light/medium/heavy)
- [ ] Verify stability rules (unstable or out-of-range → reject)

### 3) Barcode Mode
- [ ] Load barcode mapping table (hash/lane)
- [ ] Test known SKUs route correctly
- [ ] Confirm no-read behavior (warning + reject or fallback)

**Note**
- S7-1500 variant: optionally log extra fields (timestamps, scan latency) and capture trend screenshots for portfolio.

---

## H) Jam & Edge Case Tests
- [ ] Create jam scenario (block product path) → `Alm_JamDetected` triggers; safe stop
- [ ] Fast infeed to challenge min spacing → warning triggers (or controlled behavior)
- [ ] Force FIFO near full (simulation) → verify overflow fault triggers correctly
- [ ] Verify underflow protection (no pop on empty queue)

---

## I) HMI Verification
- [ ] Overview: Start/Stop/Reset, mode selection, speed, counters update correctly
- [ ] Config page: thresholds and mapping change behavior immediately (or after apply)
- [ ] Manual page: jog actions gated by safety
- [ ] Alarms page: active alarms appear, acknowledge works, reset guidance correct
- [ ] Maintenance page: cycle counts and sensor health indicators update

---

## J) Final Acceptance & Evidence Pack (for portfolio)
- [ ] Record simulation/real demo video of sorting in all modes
- [ ] Export:
	- Tag list (CSV)
	- IO list (XLSX)
	- PLC logic (PDF print)
	- HMI screenshots (PNG/PDF)
- [ ] Save evidence under `media/` and `project-files/exports/`

Acceptance Summary:
- [ ] Safety pass
- [ ] Sorting accuracy pass (color/weight/barcode)
- [ ] Fault handling pass
- [ ] HMI pass
# 05 - Commissioning Checklist

## Company Handover Quick Checklist
- [ ] Wiring verified
- [ ] I/O forced test
- [ ] Air pressure OK
- [ ] Diverters cycle test
- [ ] Sensor stability check
- [ ] Speed range validated
- [ ] Fault tests completed (E-stop, guard open, overload, jam)
- [ ] Final acceptance test results recorded

## A) Pre-Power Checks
- [ ] Mechanical installation complete and inspected
- [ ] Pneumatic lines leak-tested and pressure set
- [ ] Motor/VFD wiring verified against drawings
- [ ] I/O terminal labeling matches IO list

## B) Electrical and IO Validation
- [ ] Verify each DI transitions correctly in PLC watch table
- [ ] Verify each DO energizes correct device
- [ ] Verify AI/AO raw scaling and engineering units
- [ ] Confirm network addresses and communication stability

## C) Safety Validation
- [ ] E-stop removes hazardous motion immediately
- [ ] Guard open causes safe stop and alarm
- [ ] Safety feedback status visible on HMI
- [ ] Restart requires deliberate operator action

## D) Functional Sequence Tests
- [ ] Auto start/stop cycle validated
- [ ] Item detection and tracking stable at nominal speed
- [ ] Diverter pulse timing tuned
- [ ] Correct lane assignment for all recipes/classes
- [ ] Reject path works for unknown or invalid items

## E) Fault Injection Tests
- [ ] Start timeout test
- [ ] Jam timeout test
- [ ] Low pressure test
- [ ] Actuator return timeout test
- [ ] Alarm reset logic verified for each fault

## F) HMI and Reporting
- [ ] Counters reset and retention behavior verified
- [ ] Alarm list and history readable and accurate
- [ ] Trend page updates for speed / throughput
- [ ] Operator and maintenance pages permission-tested

## G) Handover
- [ ] Final PLC/HMI backup archived
- [ ] Tag list and IO list exported
- [ ] As-built notes documented
- [ ] FAT/SAT report signed off
