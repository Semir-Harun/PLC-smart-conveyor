# 01 - Architecture

## System Context
The smart conveyor sorting cell moves products from infeed to sorting point and diverts selected items based on classification logic.

## Components
- PLC CPU
- Remote I/O or local rack I/O
- VFD for conveyor drive
- Sensor set (photoeyes, pressure switch, optional weight)
- Diverter actuator (pneumatic or electric)
- HMI panel

## Data and Control Flow
1. Sensors provide presence and condition data.
2. PLC evaluates permissives and operating mode.
3. Part classification logic creates route command.
4. Tracking logic aligns command with physical item position.
5. Actuator command executes at sorting point.
6. HMI visualizes state, counters, alarms, and controls.

## Network Recommendation
- Industrial Ethernet backbone.
- Static IPs for PLC/HMI/VFD when required by site standard.
- Separate VLAN / security zone when integrated with SCADA.

## Design Notes
- Keep safety functions in certified safety hardware.
- Keep logic modular to simplify troubleshooting and reuse.
- Maintain one source of truth for tags and I/O documentation.
