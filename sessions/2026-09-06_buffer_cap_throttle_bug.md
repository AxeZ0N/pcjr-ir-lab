# Handoff — Buffer cap + throttle bug

Date: 2026-09-06
Scope: Phase 1 build of the buffer-overflow battery, which collapsed into
two discoveries: the INPUT line cap and the pycjr stdin throttle bug.
No new anchors. Several hardware runs; test_log included.

## Verified this session

- Ring capacity is 15 entries, empirically, on the IR path: drainless
  runs A (60 cps) and B (86 cps) both gave exactly 15/15 fill.
- Transport is clean at 86 cps with no consumer (Run B 15/15).
- Cartridge BASIC has no `TIMER` (empirical). BIOS timer counter verified
  at `0040:006C` (18.2 Hz) as the pure-BASIC fallback.
- `INPUT` line cap ~254 chars, rate-independent: 300@86 and 300@60 both
  returned `len=254`, tail dropped, Enter dropped, INPUT wedged.
- `pycjr.py` `--stdin` was unthrottled (~86 cps) and ignored `--cps`.
  Fix: throttle moved into `send_char`, `--cps` flag added. `pycjr.py` is
  a manual edit, not payload-ingestible.
- Drop reproduced once pre-fix (`40 end` -> `4nd`), rate-unattributed.
- Tiny multi-line paste clean post-fix (single run).
- Working driver is DRAINLESS.BAS v3: for-loop delay, no TIMER, empty
  ring pre-check, pointer-delta readout.

## Open questions

- Is "60 safe" still true for multi-line pastes post-fix? Only one tiny
  clean run; needs a formal re-test.
- Does "never fully recovers" have an analog (AGC) component, or is it
  fully explained by the cap/Enter wedge + unthrottled contamination?
- Exact `INPUT` cap: 254 observed; 255 vs 254 vs byte-boundary unpinned.
- Should the recovery re-test use `--stdin` or `--text`? Post-fix both
  honor `--cps`.

## Loose ends

- DRAINLESS.BAS v3 is not anchored. Pure BASIC, no machine code, no DATA
  block — recommend a .BAS-only anchor if it becomes reusable ground truth.
- DMEAS.BAS v1 dead (TIMER); v2 (BIOS counter) never built; Run C retired.
- 30k loop delay ~2 minutes; use 8k.
- Skip-repro and cap programs are ephemeral; not anchored.

## Suggested next scope

RE-DERIVE the conclusion before designing the next experiment (directive).
With the throttle bug fixed and the cap discovered, restate what
"86 drops" means. Live hypotheses, in order: (1) cap/Enter wedge on long
lines, (2) line-boundary ring flood on short-line bursts, (3) possible
analog recovery persistence. Design ONE experiment to discriminate.
Starting point: formal multi-line paste at true 60 via `--stdin` post-fix,
then the same at true 86, counting corrupted lines.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — transport regression, unchanged.
- docs/anchors/BRIDGEA.BAS / .ASM — Contract-A positive control, unchanged.
- docs/anchors/CH0CAL.ASM + .bas — functional primary regression, unchanged.
- docs/anchors/BASLOAD.BAS — sentinel loader, unchanged.
- No new anchors this session. DRAINLESS.BAS v3 pending anchor decision.
