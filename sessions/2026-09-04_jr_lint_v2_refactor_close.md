# Handoff — jr lint v2 refactor close: Phase R repair + Phase 6 regression

Date: 2026-09-04
Scope: close the jr lint v2 refactor. Phase R (repo/cache context-doc
repair + jr-ingest replacement path) and Phase 6 (two-sided regression,
negative side measured). No hardware run, no new anchors.

Reconstruction note: this handoff was lost to a facts.md scaffold leak
and is restored from the 2026-09-04 facts.md headings, not from a
captured original. See `facts_tail_scaffold_leak`.

## Verified this session

- Phase 6 negative discrimination measured: `jr build --shape bridge
  --stage 6` on `docs/anchors/IRPING2.ASM` exits 4 with exactly
  `entry` `0E1F55E8` and `epilogue` `A05DCB`; no selfloc/budget/NMI
  false positives.
- `docs/anchors/CH0CAL.ASM` shows the identical pre-Contract-A
  signature; staleness is measured, not era-suspicion.
- Contract-A entry offset measured: selfloc displacement is `R − 7`
  (disp 121 for R=128).
- MCP shape=None boundary fix verified.
- Skill set repaired: cartridge skill v7→v8, test workflow v10,
  payload v2→v3, project pointers corrected.

## Open questions

- `--rules` retirement drift: CLI rc=2 vs MCP silent-drop; neither
  matches spec §7 wording. Decision recorded to document the measured
  behavior; manual spec/manual edit pending.
- Composite binaries (NMIDISPB): the positional epilogue suffix
  matcher fails on trailing chain data. The spec offers no composite
  shape. Decide: composite shape vs lint-as-installer-only.
- Positive discrimination: no pure post-Contract-A bridge anchor
  exists in the repo; deferred to anchor regeneration.

## Loose ends

- `facts.md` tail scaffold leak (lines 2188–2198) stripped manually.
- Duplicate `jr_rules_retirement_drift` heading merged manually.
- IRPING2/CH0CAL rebuild paths blocked until hardware-backed
  regeneration.

## Suggested next scope

- Anchor regeneration for IRPING2 + CH0CAL under Contract A, with a
  hardware run and test_log entry. Separate authorized session.
- Exercise handler/iret shapes end-to-end.
- Decide the composite-shape question.

## Ground truth

- `docs/anchors/IRPING2.BAS` / `IRPING2.ASM` — pre-Contract-A; relint
  measured, regeneration pending.
- `docs/anchors/CH0CAL.BAS` / `CH0CAL.ASM` — pre-Contract-A; relint
  measured, regeneration pending.
- No new anchors; no hardware run.
