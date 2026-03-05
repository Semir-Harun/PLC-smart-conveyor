# 03 - Sequence and State Machine (Companion)

This file is retained for compatibility with earlier revisions.

## Canonical Documents
- Software architecture and block orchestration: `docs/03-software-architecture.md`
- State machine definition and restart rules: `docs/04-state-machine.md`
- Alarm model and reset policy: `docs/05-alarms.md`
- Fault and safety boundary details: `docs/04-faults-and-safety.md`

## Sequence Summary
1. Product detected at `DI_PE_Infeed`.
2. Product record created and pushed into FIFO.
3. Decision computed by configured rule priority.
4. At divert position, oldest FIFO decision is applied.
5. Counters and alarms are updated for HMI.

For implementation-level details, use the canonical documents above.