# Handoff — Linewatch repro closure + throttle verification

Date: 2026-09-07
Scope: close the skip-char reproduction question and verify the pycjr
throttle path.

## Verified this session

- Two controlled true-86 pastes on the post-fix text
# Handoff — Linewatch repro closure + throttle verification

Date: 2026-09-07
Scope: close the skip-char reproduction question and verify the pycjr
throttle path.

## Verified this session

- Two controlled true-86 pastes on the post-fix text path produced
  zero loss: a 31-line payload including the literal `40 end`
  (`mismatch=0`), and ten 40-char uniform lines (all length 40).
- The historical `4nd` drop did not reproduce. It remains
  single-occurrence and pre-fix.
- pycjr.py `send_char` throttles on both the plain and Shift paths;
  the non-tty `--stdin` paste path is paced via `send_text`.
- 86 cps is the physical make+break frame floor (~11.6 ms/char), not a
  throttle setting; at 86 `_throttle` sleeps ~0.
- Decision: `--cps` now propagates to `PCjrTestHarness` (manual edit).

## Open questions

- Whether real use stays clean at 86 over time is the only remaining
  confirmation; two synthetic shapes are not the field.
- Whether the removed Enter delay matters for line-boundary drops is
  untested because no boundary drop reproduced.

## Loose ends

- LINEWATCH.BAS and the 40-char uniform probe are ephemeral; not
  anchors.
- Non-text sender paths (`send_ansi_escape`, `send_scan`,
  `send_ctrl_break`, `send_fkey`, `send_reset`) bypass the throttle.
- DRAINLESS.BAS v3 anchor decision inherited, still pending.

## Suggested next scope

None blocking. If real use stays clean at 86, no follow-on. If a drop
reappears, capture the exact failing payload text before any synthetic
proxy.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — unchanged.
- docs/anchors/BRIDGEA.BAS / .ASM — unchanged.
- docs/anchors/CH0CAL.ASM + .bas — unchanged.
- docs/anchors/BASLOAD.BAS — unchanged.
- No new anchors this session.
