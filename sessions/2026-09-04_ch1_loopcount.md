# Handoff — CH1 loop-count leg

Date: 2026-09-04
Scope: Measure CH1 loop cost against the LOOPCOUNT CH0 baseline; disproof
of manual 1.1925 MHz; settle-NOP overshoot test (Leg B) deferred.

## Verified this session

- CH1LOOP built via jr build stage 6, N=1 and N=1000, zero errors, zero
warnings, 69 bytes each. Six CH1 edits from the LOOPCOUNT anchor,
length-identical.
- Hardware clean run both builds: status=1, RETURNED OK, loaded 69.
- N=1 dCH1=132; N=1000 dCH1=18780 (wrap corrected).
- Δ = 18.67 CH1 ticks/LOOP — matches the CH0 anchor exactly.
- Disproof verdict: failed_to_disprove. Manual 1.1925 MHz survives;
F not observed.
- DEFINT overflow in runner line 190 fixed with DEFDBL T.

## Open questions

- R~1.145 decrement-ratio conflict: fixed-overhead in the latch/read
idiom hypothesis, unverified.
- settle-NOP overshoot (Leg B) pending; BITSAMP source absent.

## Loose ends

- LOOPCOUNT.BAS anchor has the same latent DEFINT T0/T1 typing.
- CH1LOOP anchor files created this session.

## Suggested next scope

Leg B: reconstruct the BITSAMP CH1 wait-loop body, then run the
settle-NOP overshoot test against the 114-tick anomaly
(wait_overshoot_ch1_anomaly).

## Ground truth

- docs/anchors/CH1LOOP.BAS / CH1LOOP.ASM — new this session.
- docs/anchors/LOOPCOUNT.BAS / LOOPCOUNT.ASM — CH0 reference, unchanged.
- docs/anchors/IRPING2.BAS / IRPING2.ASM — regression, unchanged.