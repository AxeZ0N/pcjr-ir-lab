# Handoff — BRIDGEA hardware positive control

Date: 2026-09-04
Scope: run BRIDGEA on the PCjr as the first Contract-A machine-code
bridge positive control. IRPING2 regression first. Confirm the bridge
end-to-end on silicon.

## Verified this session

- IRPING2: status=3, keyboard intact. Transport sane.
- BRIDGEA: loaded 28 bytes, RETURNED OK, marker=66, keyboard intact.
- Contract-A bridge confirmed on hardware: entry `0E 1F 55 06`, selfloc
  disp `79`, NMI mask/clear/restore, epilogue `07 5D CB`.
- BRIDGEA graduates from lint fixture to hardware anchor.

## Open questions

- Whether to rebuild the CH1-verbatim BITSAMP decode stage under
  Contract A. Recommended next scope.
- `wait_overshoot_ch1_anomaly` remains open (114t overshoot).
- `sync_reference_phase_hypothesis` remains open.

## Loose ends

- facts.md heading `research_track_state` is still absent though the
  skill points to it. Needs its own retrieval session to populate
  properly; not created this session to avoid a thin stub.
- `anchor_identities` heading created this session.
- `bridgea_positive_control_fixture` superseded by
  `bridgea_positive_control_hw_pass`.

## Suggested next scope

Rebuild CH1-verbatim BITSAMP under Contract A via `jr build`, run with
identical Pi stimulus (`h` key), gate on h->bit1 decode with keyboard
intact. Then attack the overshoot anomaly with a disproof contract.

## Ground truth

- `docs/anchors/BRIDGEA.BAS` / `BRIDGEA.ASM` — hardware anchor
  (Contract-A).
- `docs/anchors/IRPING2.BAS` / `IRPING2.ASM` — pre-Contract-A regression.
- `docs/anchors/CH0CAL.BAS` / `CH0CAL.ASM` — pre-Contract-A.
- `docs/anchors/BASLOAD.BAS` — sentinel loader pattern.
