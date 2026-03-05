# 12 - Measured Results Template (Performance + Quality)

Use this template to document objective evidence from PLCSIM/Factory I/O and (if available) real hardware tests.

## Test Context

- Date:
- Tester:
- Project version/tag:
- TIA version:
- Target CPU: S7-1200 / S7-1500
- Scene/setup: Factory I/O scene name or real line description
- Decision mode under test: Color / Weight / Barcode

---

## A) Throughput

| Metric | S7-1200 | S7-1500 | Notes |
|---|---:|---:|---|
| Nominal speed setpoint (%) |  |  |  |
| Measured parts/min (5 min run) |  |  |  |
| Peak parts/min (stable) |  |  |  |
| OEE-style run availability during test (%) |  |  |  |

Example baseline (replace with your values):

| Metric | S7-1200 | S7-1500 | Notes |
|---|---:|---:|---|
| Nominal speed setpoint (%) | 60 | 60 | Same target for comparison |
| Measured parts/min (5 min run) | 34 | 41 | Factory I/O scene: 2 lanes + reject |
| Peak parts/min (stable) | 38 | 46 | No jams during peak window |
| OEE-style run availability during test (%) | 96.2 | 97.8 | Includes one short stop |

Method:
- Run each target for equal duration (recommended 5-10 min).
- Exclude planned stops and annotate all interruptions.

---

## B) Sorting Accuracy

| Scenario | Samples (n) | Correct | Incorrect | Accuracy % |
|---|---:|---:|---:|---:|
| Color mode |  |  |  |  |
| Weight mode |  |  |  |  |
| Barcode mode |  |  |  |  |
| Mixed stream |  |  |  |  |

Example baseline (replace with your values):

| Scenario | Samples (n) | Correct | Incorrect | Accuracy % |
|---|---:|---:|---:|---:|
| Color mode | 120 | 118 | 2 | 98.3 |
| Weight mode | 120 | 119 | 1 | 99.2 |
| Barcode mode | 100 | 99 | 1 | 99.0 |
| Mixed stream | 200 | 196 | 4 | 98.0 |

Recommended acceptance target:
- >= 98% in simulation
- >= 99% on stable real hardware (after tuning)

---

## C) Fault Handling and Recovery

| Fault | Detect time (s) | Safe stop confirmed | Reset time (s) | Recovery successful |
|---|---:|---|---:|---|
| E-stop pressed |  | Yes/No |  | Yes/No |
| Guard open |  | Yes/No |  | Yes/No |
| Jam detected |  | Yes/No |  | Yes/No |
| Air pressure low |  | Yes/No |  | Yes/No |

Example baseline (replace with your values):

| Fault | Detect time (s) | Safe stop confirmed | Reset time (s) | Recovery successful |
|---|---:|---|---:|---|
| E-stop pressed | 0.15 | Yes | 4.3 | Yes |
| Guard open | 0.20 | Yes | 5.1 | Yes |
| Jam detected | 2.95 | Yes | 7.4 | Yes |
| Air pressure low | 0.35 | Yes | 6.0 | Yes |

Notes:
- Detect time: event trigger to alarm visible.
- Reset time: operator reset action to resumed run.

---

## D) FIFO and Tracking Stability

| Check | S7-1200 | S7-1500 | Result |
|---|---|---|---|
| FIFO push/pop balanced over run |  |  | Pass/Fail |
| Overflow protection tested |  |  | Pass/Fail |
| Underflow protection tested |  |  | Pass/Fail |
| Mis-sort due to tracking delay observed |  |  | Yes/No |

Example baseline (replace with your values):

| Check | S7-1200 | S7-1500 | Result |
|---|---|---|---|
| FIFO push/pop balanced over run | Yes | Yes | Pass |
| Overflow protection tested | Yes | Yes | Pass |
| Underflow protection tested | Yes | Yes | Pass |
| Mis-sort due to tracking delay observed | No | No | Pass |

If mis-sorts occurred, record tuning actions:
- MinSpacing:
- Diverter pulse time:
- Sensor offset / delay compensation:

---

## E) HMI Usability Smoke Check

| Check | Result | Notes |
|---|---|---|
| Overview controls responsive | Pass/Fail |  |
| Config write/read works | Pass/Fail |  |
| Alarms page displays/acknowledges | Pass/Fail |  |
| Manual controls safety-gated | Pass/Fail |  |

Example baseline (replace with your values):

| Check | Result | Notes |
|---|---|---|
| Overview controls responsive | Pass | No visible lag |
| Config write/read works | Pass | Setpoints persisted during test |
| Alarms page displays/acknowledges | Pass | Ack behavior matched PLC logic |
| Manual controls safety-gated | Pass | Blocked correctly when SafeOk=0 |

---

## F) Evidence Links (Repository)

- Demo video:
- HMI screenshots:
- Exported tag list:
- Exported PLC block print:
- Exported FAT/SAT sheet:

Suggested locations:
- `media/demo-video/`
- `media/screenshots/`
- `project-files/Siemens-TIA/S7-1200/`
- `project-files/Siemens-TIA/S7-1500/`

---

## G) Summary for Recruiter/Reviewer

### Final score snapshot
- Throughput result: 
- Accuracy result: 
- Fault recovery result: 
- Overall verdict: Ready / Needs tuning

Example baseline (replace with your values):
- Throughput result: Stable at 34 ppm (S7-1200) and 41 ppm (S7-1500) at 60% speed setpoint.
- Accuracy result: 98.0-99.2% across test modes.
- Fault recovery result: All critical faults detected and recovered with safe stop behavior.
- Overall verdict: Ready for FAT demo; minor tuning optional for mixed-stream edge cases.

### Lessons learned (3 bullets)
1.
2.
3.
