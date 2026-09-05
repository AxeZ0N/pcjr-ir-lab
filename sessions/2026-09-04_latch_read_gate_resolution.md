# Handoff — Latch-read gate resolution

Date: 2026-09-04
Scope: Resolve the jr latch-read gate conflict and widen the rule to all
three 8253 counter latch commands.

## Verified this session

- jr_rules.json latch-read `a` widened from scalar B000E643 to list
  ["B000E643","B040E643","B080E643"] (CH0/CH1/CH2 latch commands).
- jr.py check_before now accepts a scalar-or-list for `a`; any preceding
  match satisfies the rule before the first bare counter read.
- Smoke lint (stage 6, shape bridge, ceiling 180): CH1 latch/read
  sequence B0 40 E6 43 E4 41 E4 41 passed with zero errors and zero
  warnings. All 11 stage-6 rules exercised.
- Policy decided: CH1 latch/read authorized iff NMI masked (out 0A0h,00h)
  first. Hazard is live keyboard NMI, not the CH1 read. Stage-5
  nmi-mask/nmi-restore rules already enforce the shape.
- BIOS 0FAB re-verified from listing: stock KBDNMI latches/reads CH1
  with the NMI latch still set (mov al,40h / out 43h / in 41h / in 41h)
  on every keystroke. manual-verified, unchanged.

## Open questions

- None blocking. The CH1 mode-3 counter decrement-rate question from the
  prior handoff remains open and is now unblocked by the tooling gate.

## Loose ends

- jr_rules.json and jr.py were patched manually; neither is in the
  ingest allowlist, so the patch rides a hand commit, not this payload.
- hardware_map TIMER0 row still carries 2.38636 MHz until superseded
  at a later ingest.
- Rule message now reads "41h reads are a keyboard hazard only with NMI
  live"; this supersedes skill Rule 6's inline hazard line in effect,
  pending a skill replacement file at a future close.

## Suggested next scope

CH1 loop-count leg: N=1 then N=1000, settle NOPs, NMI masked, against
the LOOPCOUNT CH0 baseline. Then the settle-NOP overshoot test for the
114-tick wait anomaly (open item wait_overshoot_ch1_anomaly).

## Ground truth

- docs/anchors/IRPING2.BAS / IRPING2.ASM — transport regression, unchanged.
- docs/anchors/BRIDGEA.ASM / BRIDGEA.BAS — Contract-A lint fixture, unchanged.
- docs/anchors/LOOPCOUNT.BAS / LOOPCOUNT.ASM — CH0 loop-count baseline, prior session.
- No new hardware anchor this session; no hardware run performed.
