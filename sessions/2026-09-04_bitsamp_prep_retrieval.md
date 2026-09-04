# Handoff — BITSAMP rebuild prep: retrieval

Date: 2026-09-04
Scope: retrieve KBDNMI/I30 bodies, locate BITSAMP source, split Probe B failure signatures, point next session at test design.

## Verified this session

- `KBDNMI` (3283–3408) and `I30` (3409–3449) pulled byte-exact.
- `INT 48h` at `0FF7`; comment says INT 41, disassembly wins (existing fact `int48_runtime_vector`).
- Interrupt assignment block (1344–1649) pulled; `NMI_PTR` write at `0000:0008`, `KEY62_PTR` two-write supersede.
- BITSAMP source absent (facts: `bitsamp_source_absent`).
- Probe B failure signatures split into three axes (facts: `probe_b_failure_signatures_split`).

## Open questions

- Mechanism 3 untested: does a side-effect-free delay redirect fail, and at what CH1-tick threshold?
- How does that threshold relate to the 114-tick `sync_reference_phase` overshoot? No evidence yet.

## Loose ends

- `ir_protocol_frozen` heading absent; burst/gap geometry scattered across `carrier_high_us`, `ch1_clock_clean_12th`, `pi_stop_omission`, `bare8_ceiling_revision`. Blocks the `sync_reference_phase` falsifier's static retrieval. Already implied by that fact's continuation order; recorded here for the next session's retrieval plan.
- `research_track_state` heading absent (carried from BRIDGEA handoff).
- POD NMI swap (`D11`/`F815`) retrieved but not yet a standalone fact — decide if it merits one.
- Authorization ledger gap: `rule9_nmidisp_carveout` says Probe B needed separate go-ahead, but `probe_b_noop_chain_pass` records a same-day passing run with no recorded authorization. Ledger review, not asserted as conflict.

## Suggested next scope

**Design the Probe B cycle-delay disproof test.** Design session, not build. Output is a delay-sweep redirect spec plus a run plan.

Design constraints (proposals, not locked), with sources:

1. **Register net-zero before `JMP FAR`.** Source `kbdnmi_entry_contract` (manual-verified): transparent save/restore means any touched GPR must be restored, or the run is confounded by Mechanism 1.
2. **Stack net-zero.** Source `kbdnmi_entry_contract` + `probe_b_nonzero_work_defect`: SP must be byte-identical at KBDNMI entry; any un-popped frame breaks the IRET unwind (Mechanism 2).
3. **Delay quantized in CH1 ticks.** Source `sync_reference_phase_hypothesis` + `kbdnmi_overshoot_hardware_intrinsic`: sweep the delay, find the decode-failure threshold, compare against the 114-tick overshoot before promoting any timing claim.
4. **IRPING2 gates every run.** Source `irping2_transport_regression` (Rule 5 anchor).
5. **Static prerequisite.** Assemble `ir_protocol_frozen` before any hardware dependent on burst/gap geometry. Source `sync_reference_phase_hypothesis` continuation order.

## Ground truth

- `docs/anchors/BRIDGEA.BAS` / `BRIDGEA.ASM` — Contract-A positive control.
- `docs/anchors/IRPING2.BAS` / `IRPING2.ASM` — transport regression, pre-Contract-A.
- `docs/anchors/CH0CAL.BAS` / `CH0CAL.ASM` — pre-Contract-A.
- `docs/anchors/BASLOAD.BAS` — sentinel loader.
- `docs/anchors/NMIDISP.*` / `NMIDISPB.*` / `NMIPEEK.*` — 08-31 dispatch probes.

No new anchors. BITSAMP intentionally has none.