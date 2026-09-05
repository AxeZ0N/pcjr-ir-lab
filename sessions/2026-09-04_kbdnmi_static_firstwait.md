# Handoff — KBDNMI static first-wait disproof

Date: 2026-09-04
Scope: static disproof of overshoot origin via byte-exact listing retrieval. No hardware.

## Verified this session

- 0FC6 BA 0220 = MOV DX,544; no 658 anywhere in KBDNMI/I30.
- DI capture is at I5, before I6; I6 is a post-capture glitch filter.
- sync_reference_phase_hypothesis superseded (I6 premise wrong).
- 0FC6 comment self-contradicts on CH1 tick rate.

## Open questions

- True CH1 effective tick rate: 0.838 µs vs the ~0.570 µs implied by "310 µs"?
- If 0.838 µs confirms, where does the 114-tick clone-side slip originate?

## Loose ends

- ir_protocol_frozen prerequisite removed for the overshoot path.
- research_track_state heading still absent.
- POD NMI swap (D11/F815) still unrecorded as a fact.
- Probe B authorization ledger gap carries forward.

## Suggested next scope

**CH1 effective-rate measurement.** Latch CH1, NOP a known cycle count,
re-latch, read decrement; derive ticks per CPU cycle against 0.838 µs.
Rule 6 hazard governs: mask NMI, latch/read 41h only with A0h D7 clear,
restore 80h before RETF. IRPING2 gates every run. Design session first.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — transport regression.
- docs/anchors/BRIDGEA.BAS / .ASM — Contract-A positive control.
- docs/anchors/CH0CAL.BAS / .ASM — pre-Contract-A.
- docs/anchors/BASLOAD.BAS — sentinel loader.

No new anchors; no hardware run.