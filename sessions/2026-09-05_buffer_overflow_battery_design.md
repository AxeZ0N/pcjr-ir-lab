# Handoff — Buffer overflow battery design (pending lock)

Date: 2026-09-05
Scope: design-only session. Closed drop-site scoping and break
disposition; produced the specification for a three-run disproof
battery for the hypothesis "observed skips at 86 ch/s are ring
overflow." No machine code, no hardware run, no new anchors,
no test_log.

## Verified this session

- No BASIC units written; no anchors created. Code authorship is
  deliberately deferred to the build-and-verify phase. The battery
  below is a specification, not a runnable unit.
- Overrun drop (`KB_INT` 157A) unreachable on the IR chain. See facts
  `overrun_drop_unreachable_ir`.
- Ordinary-key break disposition confirms 1 entry/char across both
  extended (`1107`) and ordinary (`1276`→`IRET`) paths. See facts
  `break_disposition_ordinary_keys`.
- `KB_NOISE` has 9 callers; only `1122` (full) and `157A` (overrun)
  are drops; only `1122` is reachable via IR. Beep would be a clean
  discriminator on the IR path but is unusable: no mic, and a 38 ms
  drop cadence fuses into one continuous tone. Recorded as a decision,
  not an assumption.
- Re-verified ring anatomy (15 entries, wrap `001E↔003E`, sentinel at
  `003E`, `HEAD==TAIL` ⇒ empty) and the tail-freeze-at-full fact
  (`1116 CMP` precedes `1135` write; drop path `IRET`s before `1137`).

## Open questions

- TIMER granularity ~55 ms (18.2 Hz). A clean `D` needs a ≥5 s paste
  (~430 chars) or CH0 bridge precision. Unresolved; carried into the
  build phase.
- `DEF SEG=&H40` / `PEEK` of BIOS data area semantics on Cartridge
  BASIC — `; VERIFY:` before relying.
- NMI vs consumer race: `INT 16h` uses `CLI` (masks INTR, not NMI);
  `KEY62_INT` can interrupt a ring read. Biases `D` downward (toward
  overflow), cannot fabricate a false no-overflow. Known-unknown,
  non-fatal, entirely absent in Runs A/B (no consumer).
- Inherited: `nmi_chain_detail_pointer_drift`,
  `kbdnmi_ch1_latch_conflict`.

## Loose ends

- base26 transcription dropped for this test. Tokens are two small
  decimal offsets (`H=`, `T=`) plus one `D` integer (55–90). base26 is
  reserved for edge-clock runs.
- In-session `KB_NOISE` audit was initially over-scoped to 9 callers;
  corrected to reachable-on-IR scope (2 drop sites, 1 reachable).

## Suggested next scope — phased plan

The battery is a specification only. Execution is staged into three
phases, each gated on the prior. Nothing below is locked until a run
passes its clean-run criterion.

### The specification (reference for all phases)

Hypothesis: observed skips at 86 ch/s are ring overflow, fully decided
by the consumer drain rate `D`. Overflow iff `D(R) < R`; onset
`15/(R − D)`.

**Run A — isolated capacity (the lemma).** Pi sends `h`×30 at 60 ch/s.
BASIC busy-waits — no input statement, no drain — for the full send
window, then prints `BUFFER_HEAD` and `BUFFER_TAIL` as raw decimals
from `0040:001A` / `0040:001C`. Expect `H=30 T=62`. Report raw
offsets, never a computed count; the one-slot sentinel makes
`(T−H)/2` ambiguous at full.

**Run B — transport at 86.** Same drainless shape at 86 ch/s.
`T=62` ⇒ transport clean at 86 in isolation; `T<62` ⇒ upstream loss
at 86.

**Run C — the crux.** Measure `D` at 86, not extrapolated from 60.
Time an `INPUT` return over `h`×N + single Enter. Overflow predicate
is `D(86) < 86`.

**Lemma and falsifier.** Lemma: "No consumer drain ⇒
normal-operation buffer overflow." Assumptions: A1 capacity 15;
A2 1 entry/char; A3 drop freezes tail; A4 overrun unreachable;
A5 Pi delivers ≥16 frames (log proves count); A6/A8 busy-wait runs no
input statement; A7 `PEEK` non-destructive; A9 ring verified empty
pre-send. Falsifier: after a clean drainless send of 30 at 60 ch/s,
`BUFFER_TAIL != 62`. Clean-run: IRPING2 green; Pi log shows exactly
30; ring empty pre-send; keyboard alive post-run; NMI restored.

**Protocol guards.** Drain the arming keystroke before arming; drain
or reboot between runs. Restore NMI after each run: dummy
`IN AL,0A0h`, then `OUT 0A0h,80h`. Payload char is `h`: non-modifier,
non-toggle, non-special; Shift/Ctrl/Alt makes update `KB_FLAG` and do
not enter the ring; Enter is reserved as the Run C terminator.

**Informational gain — no dead ends.** `T=62` (A): capacity and
isolated-overflow signature confirmed. `T<62` (A): hidden consumer,
capacity misread, or upstream loss at 60 — the last supersedes the
entire "60 is safe" premise. `T=62` (B): 86-clean in isolation; drop
is consumer-side. `T<62` (B): transport loss at 86. `D(86)≥86` with
real drops: overflow impossible; contradiction localizes to
NMI/consumer interaction. `D(86)<86`: overflow confirmed; onset
`15/(86−D)`.

**Fallback.** Run A failure is the finding: re-evaluate "60 safe"
before anything else; no code change. Run B 86-loss pivots to the
AGC-boundary question; Run C still runs. Run C `D≥86`-with-drops makes
the NMI/consumer interaction the next scope, with Runs A/B as anchors.

### Phase 1 — build & verify (next session)

Author `DRAINLESS.BAS` (Runs A/B) and `DMEAS.BAS` (Run C). Write a
contract block for each; generalize per routine — do not copy
IRPING2's expectations into a drainless run. Gate: code reviewed
against the spec above; `; VERIFY:` items resolved or flagged; no
hardware attempted. Phase 1 produces no anchors and no test_log.

### Phase 2 — run (following session)

IRPING2 regression first, then A, B, C in order. Record raw `H=`,
`T=`, `D=` decimals only. Gate: raw results in hand with Pi send log
and IRPING2 pass noted; no interpretation in this phase.

### Phase 3 — interpret (after Phase 2, or later if triage needed)

Apply the informational-gain table and fallback branches. If results
are unexpected, stop and triage before recording any verdict; do not
force the overflow conclusion. Successful disproof entries go to
facts and test_log; anchors are created only for programs that pass
hardware in this phase.

## Ground truth

- docs/anchors/IRPING2.BAS / .ASM — transport regression (unchanged)
- docs/anchors/BRIDGEA.BAS / .ASM — Contract-A positive control
  (unchanged)
- docs/anchors/CH0CAL.ASM + .bas — functional primary regression
  (unchanged)
- docs/anchors/BASLOAD.BAS — sentinel loader (unchanged)
- No new hardware-passed program this session; no new anchors.
