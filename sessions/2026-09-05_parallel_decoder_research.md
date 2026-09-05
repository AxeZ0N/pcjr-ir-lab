# Handoff — Parallel keyboard routine research

Date: 2026-09-05
Scope: Research the multi-threading of the keyboard receive path. This
session established the ceiling and the reframing; the next session is
source study and measurement, not construction.

## Verified this session

- 60 ch/s is the empirically safe floor (user field report); 86 ch/s
  drops near-deterministically. No systematic rate sweep exists.
- 86 ch/s serial ceiling re-derived: full frame 5823 us, make+break
  11646 us/char.
- 60 ch/s effective gap ~4010 us (throttle slack); 86 ch/s effective
  gap ~1492 us (banked 1500 us, zero slack).
- At 86 ch/s CPU hostage ~82.5%, compounding AGC-boundary pressure
  with consumer starvation.
- Parallel decode buys zero direct chars/sec; it is enabling, not a
  throughput feature.
- No type-ahead buffer fact exists; the buffer is unmeasured and is
  the leading drop candidate.

## Open questions

- Which mechanism produces the deterministic 86 drop: buffer overflow,
  NMI re-arm/latch settle at the 1500 us gap, or BASIC intake stall?
- What is BASIC's actual intake/echo rate at 60 vs 86?
- Type-ahead buffer size/location/capacity at 40:1A/1C and 40:1E/1F?
- Effective gap and CPU hostage at rates between 60 and 86?

## Loose ends

- 114-tick overshoot (overshoot_settle_nop_hypothesis) open; coupled to
  latch-settle at the 1500 us gap.
- latch_read_gate_conflict open.
- research_track_state heading still absent.
- RECOVERY4 open items: R4_180 fusion post-reset, R4_220 L7=133.

## Suggested next scope — research the multi-threaded keyboard

Frame as source study plus two no-machine-code measurements. Do not
design or build the decoder this session.

Research questions, in priority order:

1. CPU-cost map of KBDNMI. Static analysis of the already-peeked body
   (0F78–102F) and I30/I31 (1031+): where exactly the busy-wait lives,
   how much is the 5-sample windows vs the 544/526-tick waits, and what
   a poll-only-between-edges version would actually save.
2. Latch re-arm timing. What the 8255 PC0 latch requires between
   NMI-off and the next edge. Manual 2-30 bit assignments; cross-check
   against the NMI chain facts and the 114-tick overshoot.
3. Buffer anatomy. Does KEY62_INT (F000:10C6, DS=0040h) write the
   standard 40:1E/1F buffer? What is its capacity? Can a custom decoder
   write it directly without taking the NMI hostage?
4. What S2V1 and S4A already proved about masked-NMI edge capture.
   Which pieces of a parallel receiver have already passed hardware?
5. Interruptibility. Does any existing BIOS path poll PC6 between edges
   with NMI masked only briefly, or is it all busy-wait? Any precedent
   in the listing for a cheap re-arm loop?

Two measurements gate everything and need no new machine code:

- Buffer pointers: read 0040:001A/001C before and after a fast paste.
- BASIC intake: time a long INPUT echo at 60 vs 86 ch/s.

Contract (proposal, not locked):
{
  "id": "consumer_starvation",
  "hypothesis": "H — the 86 drop is consumer-side (BASIC/buffer), not transport.",
  "falsifier": "F — buffer stays empty while drops still occur at 86.",
  "clean_run": "S — buffer pointer delta and drop position both recorded on the same run.",
  "verdict": "pending"
}

## Ground truth

- docs/anchors/IRPING2.BAS / IRPING2.ASM — transport regression.
- docs/anchors/BRIDGEA.BAS / BRIDGEA.ASM — Contract-A positive control.
- docs/anchors/CH0CAL.ASM + docs/anchors/AGCPROBE.BAS — AGC probe.
- No new hardware-passed program this session; no new anchors.
