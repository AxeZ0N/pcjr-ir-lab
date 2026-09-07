# Handoff — Paste payload generation + wrap boundary

Date: 2026-09-07
Scope: generate a large multi-line paste stress payload and pin the
40-char screen-wrap boundary.

## Verified this session

- Paste payload generator produced in two shapes: string-assignment
(`<lineno> A$="P00NNxxx..."`) and dense (`<lineno> m=<lineno>:a0=0:...`),
every line-number and marker embedded for drop detection via LIST.
- 40-char boundary is a screen wrap, not corruption: at exactly 40 the
cursor wraps and the next line renders blank. Lines must total <=39.
- 3-digit line numbers push the head length; filler must be computed
from the actual head, not a fixed count.
- Post-reboot multi-line paste at true 86 cps: no corruption, keyboard
alive.

## Open questions

- Root cause of `paste_corruption_memory_state` line-table injection
remains open. The post-reboot clean run is consistent with
reset-clears-contamination but does not disprove it.

## Loose ends

- No new anchors. The generator is Pi-side tooling, not a BASIC anchor.
- The 39-char ceiling is empirical to this screen mode; not
manual-verified against a video-mode spec.

## Suggested next scope

- Run the corrected 39-char payload (2- and 3-digit lines) post-reboot
at 86 to verify every line including the 3-digit head adjustment.
- Then attack paste throughput: dense framing-only ~2x remains blocked
on the edge-triggered decoder prerequisite.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — unchanged.
- docs/anchors/BRIDGEA.BAS / .ASM — unchanged.
- docs/anchors/CH0CAL.ASM + .bas — unchanged.
- docs/anchors/BASLOAD.BAS — unchanged.
- No new anchors this session.