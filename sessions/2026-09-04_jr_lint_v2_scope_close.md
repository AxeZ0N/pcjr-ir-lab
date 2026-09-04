# Handoff — jr lint v2 scope close

Date: 2026-09-04
Scope: close the jr lint v2 refactor scope. Record the declined anchor
regeneration decision, add the BRIDGEA positive-control lint fixture,
mark the refactor spec complete. No hardware run.

## Verified this session

- `jr build --shape bridge --stage 6 --result 128` on the BRIDGEA
  source: status pass, zero warnings, all eleven rules active. 28
  bytes, entry `0E 1F 55 06`, selfloc disp `79` (R−7 = 121), epilogue
  `07 5D CB`.
- NDISASM review byte-exact; no hand-rolled bytes.
- Spec §3.3 skeleton re-read in full; builds clean against it.

## Open questions

- Whether BRIDGEA should ever graduate to a hardware anchor. Out of
  scope for this session; it remains lint-fixture class.
- Whether the refactor spec file should be physically archived or left
  in place as completed documentation. Decision recorded that it is
  superseded by `docs/jr_tool_spec.md`; file disposition not set.

## Loose ends

- Composite-shape question (NMIDISPB) remains open; not a linter
  defect, not needed for current work.
- handler/iret shapes unexercised end-to-end; synthetic fixtures
  would close this without hardware.

## Suggested next scope

Hardware/BIOS R&D resume: run BRIDGEA on the PCjr as the first
positive control, then return to the BIOS/NMI research track. IRPING2
first on transport suspicion.

## Ground truth

- `docs/anchors/BRIDGEA.ASM` / `BRIDGEA.BAS` — lint fixture; NOT a
  hardware anchor.
- `docs/anchors/IRPING2.BAS` / `IRPING2.ASM` — pre-Contract-A; exit 4
  measured, regeneration declined.
- `docs/anchors/CH0CAL.BAS` / `CH0CAL.ASM` — pre-Contract-A; exit 4
  measured, regeneration declined.
