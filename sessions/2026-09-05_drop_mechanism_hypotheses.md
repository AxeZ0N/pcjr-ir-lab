# Handoff — Drop-mechanism hypotheses and buffer anatomy

Date: 2026-09-05
Scope: source study of the keyboard receive path; resolve the type-ahead
buffer anatomy and reframe the 86 ch/s drop as three testable hypotheses.
No machine code, no hardware run.

## Verified this session

- `KEY62_INT` spans `F000:10C6–131E` (listing 3621–3921).
- Type-ahead ring is the stock 15-entry layout: head `0040:001A`, tail
`0040:001C`, buffer `0040:001E` (16 words, wrap `001E↔003E`).
- Break codes are discarded at `1107 IRET`; ring fills 1 entry per character.
- Full path (`tail+1 == head` after K4): `KB_NOISE` beep + character dropped,
never written. The full path is the only `KB_NOISE` caller on this branch,
so beep ⇒ overflow is a clean discriminator.
- The prior `typeahead_buffer_unmeasured` open item is resolved and superseded.

## Open questions

- What is BASIC's actual drain rate `D` (entries/s) at 60 vs 86 ch/s?
- At 86, do drops coincide with beeps (overflow) or arrive beep-free
(transport/upstream)?
- What is the Pi's actual send interval at nominal 86? The Pi is the
unverified input; a "drop" may be Pi-side jitter, not PCjr-side.

## Loose ends

- Q2 (latch re-arm timing, manual 2-30) not retrieved this session.
- Q5 (BIOS cheap re-arm precedent between edges) not retrieved.
- Inherited open: `kbdnmi_ch1_latch_conflict`, `nmi_chain_detail_pointer_drift`,
`overshoot_settle_nop_hypothesis`.
- `facts.md` integrity: leak #1 (1768–1813) unrecorded; leak #2 repair text
stale. Manual repair required; not done here.

## Suggested next scope — controlled reproduction + echo-rate measurement

Prerequisite: reproduce the drop under control before running any contract.
Sweep 60 → 86 ch/s in small steps on a fixed 200-char paste; record onset
rate, drop count, beep count; log Pi send timestamps. Regression IRPING2
first.

Measure `D` directly first — one number predicts drop onset and settles the
consumer-side story:

- Time a long `INPUT` echo at 60 vs 86; `D` = chars / elapsed.
- If `D = 60`, first drop at `15 / (86 − 60) ≈ 0.58 s` (~50 chars in), then a
drop every ~38 ms — dense, early, beep-correlated.

Ring read (no machine code, Cartridge BASIC):

```
10
```

`; VERIFY: DEF SEG/PEEK segment semantics and MOD arithmetic against the
PCjr BASIC Reference before relying.`

Three contracts, proposed and pending (not locked — no future spec is
locked unless the user says "lock it"):

- `transport_delivery` — H: transport delivers every sent char into the ring.
F: tail-advance count over a clean paste is less than sent count, with the
ring never observed full and zero beeps.
- `consumer_saturation` — H: BASIC drains slower than the producer fills, so
drops coincide with full-ring beep+discard. F: a drop at 86 with no beep and
ring non-full at drop time, transport already sane.
- `read_layer_loss` — H: chars reach a non-full ring yet BASIC still loses
them. F: all sent chars appear in output, zero beeps, ring drains to empty.
Lowest prior; only in play if transport passes and saturation's falsifier
fires.

## Ground truth

- docs/anchors/IRPING2.BAS / IRPING2.ASM — transport regression.
- docs/anchors/BRIDGEA.BAS / BRIDGEA.ASM — Contract-A positive control.
- docs/anchors/CH0CAL.ASM + CH0CAL.bas — functional primary regression.
- docs/anchors/BASLOAD.BAS — sentinel loader reference.
- No new hardware-passed program this session; no new anchors.