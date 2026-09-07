# Handoff — Latch gate pass + settle-NOP disproof

Date: 2026-09-07
Scope: masked CH1 latch/read safety and the settle-NOP overshoot
disproof.

## Verified this session

- Masked CH1 latch/read is live-safe: three runs returned 18018,
  15866, 17306; keyboard alive after each; loaded 41 bytes byte-exact.
  Backs latch_read_gate_resolution with hardware evidence.
- Settle-NOP disproof failed to disprove: overshoot 88 (0 NOP),
  98 (2 NOP stock), 130 (8 NOP), four runs each. Overshoot moves
  monotonically with NOP count; hypothesis remains open.
- jr stage model: latch-read is now an idiom check, not stage-gated;
  stage=6 build with masked IN 41h passed with zero warnings.
- Paste corruption cleared by Ctrl-Alt-Del soft reset; clean full
  41-byte paste at 86 cps afterward.

## Open questions

- Where is the ~17-tick gap between the stock single-wait overshoot
  (98) and the historical 114-116? Entry/trailing-edge overhead is the
  candidate, unmeasured in this probe.
- Is the overshoot phase-amplified by loop tick quantization (the
  ~5 ticks/NOP vs ~1.5 predicted spread)?

## Loose ends

- seednop A/B/C are characterization probes, not anchors; do not
  anchor them.
- `wait` is a reserved UASM mnemonic; the wait loop label is `wloop`.
- Non-text sender paths are unthrottled by decision
  (nontext_senders_unthrottled_intentional).

## Suggested next scope

- Edge-sync probe: arm on the start burst, latch CH0 on bit0's own
  rising edge, sample relative to that edge. Removes the trailing-edge
  seed and the phase-quantization class from the decode path.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — unchanged.
- docs/anchors/BRIDGEA.BAS / .ASM — unchanged.
- docs/anchors/CH0CAL.ASM + .bas — unchanged.
- docs/anchors/BASLOAD.BAS — unchanged.
- No new anchors this session.
