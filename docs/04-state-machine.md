# 04 - State Machine

## States
- `S0_STOPPED`
- `S1_READY`
- `S2_AUTO_RUN`
- `S3_SORT_ACTUATE`
- `S4_FAULT`
- `S5_MANUAL`

## Transition Highlights
- `S0_STOPPED -> S1_READY`: safety/permissives healthy
- `S1_READY -> S2_AUTO_RUN`: start command in Auto mode
- `S2_AUTO_RUN -> S3_SORT_ACTUATE`: product at divert position and decision available
- `S3_SORT_ACTUATE -> S2_AUTO_RUN`: pulse complete and actuator feedback ok
- `ANY -> S4_FAULT`: critical fault detected
- `S4_FAULT -> S1_READY`: reset command + fault removed + stop state confirmed
- `S1_READY <-> S5_MANUAL`: manual mode select/exit with permissives

## Restart Rule
If safety chain breaks, system enters `S4_FAULT` and latches.
Reset is only valid when:
- safety chain restored,
- reset command present,
- machine is in stop-safe condition.
