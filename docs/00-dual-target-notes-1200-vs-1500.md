# Dual-Target Notes: S7-1200 vs S7-1500 (TIA Portal)

## Purpose
This project is implemented for two Siemens PLC families to demonstrate portability and engineering judgement:
- **S7-1200** (compact machine cell)
- **S7-1500** (higher performance production line)

The **functional behavior is identical** in both variants:
- Same sorting modes (barcode / color / weight)
- Same state machine (IDLE → READY → RUNNING → STOPPING → FAULT)
- Same FIFO-based product tracking concept
- Same HMI pages (Overview / Config / Manual / Alarms / Maintenance)
- Same alarm philosophy (warnings vs faults, latching, reset rules)

Only capacity, diagnostics depth, and some engineering choices differ.

## Assumed Hardware Profiles (Example)
### S7-1200 Variant
- CPU: S7-1212C / S7-1214C
- HMI: KTP Basic (e.g., KTP700 Basic)
- Typical use: single conveyor + diverter station, compact panel, limited IO

### S7-1500 Variant
- CPU: S7-1511 / S7-1513
- HMI: Comfort Panel (e.g., TP700/TP900 Comfort)
- Typical use: multi-station line integration, richer diagnostics, future expansion

## Engineering Differences (What changes and why)

### 1) FIFO Size & Metadata
Both use the same UDT product record and queue logic, but sizing differs:
- **S7-1200**
  - FIFO size: **50**
  - Record fields: minimal (Id, ColorId, WeightKg, BarcodeOk, BarcodeHash, Decision)
  - Rationale: compact memory footprint and simpler debugging

- **S7-1500**
  - FIFO size: **200**
  - Record fields: may include extra diagnostics (timestamp, travel time, sensor quality flags)
  - Rationale: supports longer conveyors, more buffering, and deeper traceability

> NOTE: Queue overflow is treated as a **FAULT** in both variants.

### 2) Diagnostics & Traceability
- **S7-1200**: focus on essential alarms, counters, and simple maintenance checks
- **S7-1500**: adds optional alarm history, per-sensor last-trigger times, cycle counts, and more detailed root-cause grouping

---

### 3) Data Blocks & Access Style
- Both variants use symbolic tags and structured DBs:
  - `DB_Params`, `DB_FIFO`, `DB_Alarms`, `DB_Counters`, `DB_HMI`
- **Recommended**: keep DBs “optimized access” and do all mapping symbolically.
- Documentation exports (tag lists, IO lists, ladder PDFs) are provided for review.

---

### 4) Scan Time / Timing Strategy
- Sorting actuation is based on:
  - **rising-edge detection** of sensors
  - **TP pulse timers** for solenoids (configurable `PT`)
- **S7-1500** typically provides more scan-time headroom for:
  - more products in flight
  - more diagnostics
  - more communications

---

### 5) Communications / Networking
- Both variants assume **PROFINET** is available when required.
- Barcode/color devices can be modeled as:
  - IO-Link → PROFINET master
  - Ethernet-based scanner
  - or simulated via HMI for demonstration

The comm method does not affect the core sorting logic—only the “sensor input” interface.

---

## Portability Guidelines
To keep logic portable across S7-1200 and S7-1500:
- Use IEC timers/counters (TON/TOF/TP) and simple FB/FC separation
- Avoid CPU-specific technology objects unless you document them
- Keep a single “interface layer” for:
  - sensor acquisition (`FB_Sensors`)
  - actuator commands (`FB_Actuators`)
  - HMI mapping (`FB_HMI_Interface`)

---

## Validation
Both variants must pass the same acceptance criteria:
- Start/Stop/Reset sequences correct
- Safety interlocks stop motion and require reset
- Sorting decisions match configuration
- Reject handling works for unknown/no-read
- Jam detection triggers controlled stop + alarm
- FIFO never goes negative; overflow triggers alarm and safe stop

See:
- `docs/05-alarms.md`
- `docs/05-commissioning-checklist.md`
# 00 - Dual-Target Notes (S7-1200 vs S7-1500)

## Intent
This project keeps one control concept and one software architecture while deploying to two Siemens CPU families:
- S7-1200 (compact machine style)
- S7-1500 (higher performance line style)

## What stays the same
- Same FB/FC architecture
- Same UDT product record
- Same FIFO algorithm and sorting decision logic
- Same HMI screen concept
- Same safety interlocks and alarm philosophy

## What differs
- Performance and memory headroom
  - S7-1200: compact and cost-efficient for machine cells
  - S7-1500: more headroom for tracking depth, diagnostics, and expansion
- Optimized DB access and symbolic addressing
  - Both support symbolic/optimized workflows
  - S7-1500 projects typically lean more heavily on full symbolic engineering
- Technology features
  - S7-1500 generally offers more advanced technology options
  - This project stays IEC-standard for portability
- Communications context
  - Both support PROFINET
  - S7-1500 is typically used in larger networked lines; S7-1200 in compact machines

## CPU assumptions (default profiles)
### S7-1200 profile
- CPU: S7-1214C (or S7-1212C)
- HMI: KTP700 Basic
- VFD: G120 or equivalent (PROFINET or analog speed reference)

### S7-1500 profile
- CPU: S7-1511 or S7-1513
- HMI: TP700/TP900 Comfort
- VFD: G120/SINAMICS with extended diagnostics

## FIFO sizing policy
- S7-1200: `50` products
- S7-1500: `200` products plus optional metadata (timestamp, travel time, diagnostic flags)

Same logic, different capacity.

## Shared block architecture
- OBs: `OB1_Main`, `OB100_Startup`
- FBs: `FB_SafetyInterlocks`, `FB_ModeManager`, `FB_ConveyorControl`, `FB_ProductTrackingFIFO`, `FB_DiverterActuation`, `FB_AlarmsAndCounters`
- FCs: `FC_ComputeDecision`, `FC_ScaleLoadCell`
- DBs: `DB_IO`, `DB_HMI`, `DB_Params`, `DB_FIFO`, `DB_Alarms`, `DB_Counters`
