# Handoff — CH1 rate + CH0 loop-count

Date: 2026-09-04
Scope: Resolve CH1 tick-rate conflict; verify the measurement instrument; measure CH0 rate and LOOP cost.

## Verified this session

- CH1 @ D5=0 = 1.1925 MHz -> 0.838 us/tick, manual-verified (2-35:21-25).
  0FC6 "310 USEC AWAY" is the outlier; 544 ticks = 456 us.
- CH0 = 1.19318 MHz corroborated by loop-count: 18.67 CH0 ticks/LOOP,
  74.7 CPU cycles/iter against a 17-cycle nominal LOOP.
- Loop cost measured: ~74.7 cycles bare LOOP.
- CH1RATE 0.939/0.937 wildcard was an inverted down-counter subtraction.
  True decrements CH0=19310, CH1=22108, R~1.145.
- LOOPCOUNT CH0 probe is clean with settle NOPs and Contract-A frame.

## Open questions

- Does the CH1 mode-3 counter register decrement at the input rate
  (manual implies yes; direct measurement blocked by latch gate)?
- Is the settle-NOP omission the cause of the 114-tick overshoot?
  (open item overshoot_settle_nop_hypothesis)

## Loose ends

- jr latch-read gate conflicts with BIOS 0FAB and ch1_masked_read_safe.
  Policy decision pending before any IN 41h probe.
- hardware_map TIMER0 row still carries 2.38636 MHz until superseded
  at ingest.
- CH1RATE has no retype source; not worth anchoring given the artifact.

## Suggested next scope

Resolve the latch-read gate conflict, then run the CH1 loop-count leg
(N=1 then N=1000, NOPs, masked) and the settle-NOP overshoot test.

## Ground truth

- docs/anchors/LOOPCOUNT.BAS — CH0 loop-count runner, N=1000 reference.
- docs/anchors/LOOPCOUNT.ASM — design logic.
- Regression: IRPING2 (unchanged).
