# Handoff — Token
# Handoff — Token-density rungs + throttle completion

Date: 2026-09-06
Scope: Disprove token-density-driven drop risk at 60 and 86 cps, and
close the incomplete plain-path throttle fix in pycjr.py.

## Verified this session

- pycjr.py `send_char` plain-char branch now calls `_throttle()`;
  both branches pace at `chars_per_sec`. Code-verified via grep_repo.
  User committed the fix manually (bin/ is not payload-ingestible).
- The "86" rate is the sender's native make+break floor
  (~11.6 ms/char), not a throttle setting. Historical 86 observations
  ran unthrottled.
- tokdensity60: 40x27-char program paste, sparse S and dense C, both
  clean at true 60. Density drop disproved.
- tokdensity86: same at true 86, both clean. Density drop disproved.

## Open questions

- Does the original line-start drop (`drop_line_boundary_repro`,
  `40 end` -> `4nd`) reproduce under a correct throttle? Both
  confounds (native-floor rate, removed enter delay) are now removed;
  a controlled 27-char run did not reproduce it. Longer or more
  realistic payload shapes untested.
- V3 analog recovery persistence remains untested against the software
  wedge (cap-breach + Enter-drop).

## Loose ends

- tokdensity payloads S and C are ephemeral stimuli, not anchors.
- The enter delay was removed in eac8fb6 and not restored; whether it
  matters for line-boundary drops is part of the open question.

## Suggested next scope

Re-run the original multi-line program paste (the `40 end` repro shape)
at controlled 60 and 86, with the enter-delay question decided
explicitly. One experiment to close `drop_line_boundary_repro`.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — transport regression, unchanged, not exercised.
- docs/anchors/BRIDGEA.BAS / .ASM — Contract-A positive control, unchanged, not exercised.
- docs/anchors/CH0CAL.ASM + .bas — functional primary regression, unchanged, not exercised.
- docs/anchors/BASLOAD.BAS — sentinel loader, unchanged, not exercised.
- No new anchors this session. tokdensity payloads are not anchor candidates.
