# Spec — ⑤ size-limits table in fish ID

## Goal
One compact static table in the fish ID section (KNOW): can you keep this fish, and at
what size? Visible, not collapsed — the reader is holding a flapping fish; a toggle is
the wrong place for a legal minimum. Zero JS, zero storage, zero network: pure HTML that
inherits night/print/offline for free.

## The provenance rule (the whole item, really)
**No remembered number ships.** Every size in the table must be transcribed at build
time from a primary source fetched during the build — NWIFCA byelaws (the district's
minimum sizes) and gov.uk (national bass rules) — and the build report must show each
value beside the URL and the words it came from. A species whose minimum cannot be
verified against a primary source that day ships as **"check NWIFCA ↗"** with the link,
never a from-memory value — the Decision A pattern applied to regulation: an invented
legal minimum could talk someone into keeping an undersized fish. The stamp under the
table reads "verified against <named sources> <date>" and the date-stamp rule applies:
only sources actually read that day get named.

## Rows and columns
Rows = exactly the species the guide's calendars already name (the `speciesList()`
union: bass, codling, dab, eel, flounder, mackerel, mullet, plaice, whiting) — no new
species (rejected list). Columns: species · minimum size (or "none set" where that is
the sourced truth — never an invented "sensible minimum") · notes. Notes carry only
already-established facts: eel = always released, protected (no size — the law is
release); bass = the PLAN regs-box rules, restated numerically identically and
cross-checked at build (the two must never disagree). Weever gets one row outside the
size logic: never keep, never handle — pointer to the first-aid card.

## Copy that travels with the table
- How to measure: nose tip to tail tip, fish flat, mouth closed — one line.
- Undersized = back quickly; pointer to the existing aftercare steps in DO.
- Scope: these are the rules for the English NW coast this guide covers; the Welsh
  shore of the Dee is Natural Resources Wales water with its own rules — check before
  crossing. One sentence, no NRW table (out of scope).
- Rules change: reviewed annually, link NWIFCA + gov.uk (same links as the regs box).

## Acceptance criteria
- Build report lists every table value with source URL + quoted source text; any
  unverifiable row demonstrably fails closed to the "check" link.
- Bass row == regs box (grep both, numbers identical). Eel row has no size, says release.
- No `<script>` change, no new listeners, no storage keys, no fetches at any point.
- Table prints (it is not inside a details), legible in night mode, present JS-off.
- Existing copy nowhere contradicted (grep 42 cm, eel, releases).

## Out of scope
NRW/Welsh rules beyond the one-sentence pointer · bag limits and closed seasons (they
live in the regs box; the notes column points there) · size→weight tables · new species ·
fish-welfare essay · any JS.

## On pass (same commit)
CLAUDE.md: tick size-limits; record the fail-closed rule for regulatory numbers (no
remembered values, per-value source quotes in the build report) and the bass
row/regs-box identity constraint.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

Static "Can I keep it?" table in fish ID (`#fishid`), **visible not collapsed** — a legal
minimum behind a toggle is the wrong disclosure level. Zero JS, storage or network.

### The fail-closed rule for regulatory numbers
**No remembered value ever ships.** Every size was transcribed at build time from raw
NWIFCA/gov.uk HTML, and the review re-fetched and re-matched every number independently.
**Summarisers are NOT a source** — the review caught a fetch model inverting the
Schedule 2 heading. (This rule was later extended to medical content; see
`hook-in-skin-first-aid.md`, where it bit four times.)

### The sourcing record
Verbatim, on Schedule 2's scope:

> "Schedule 2 also applies to all persons fishing in the district, unless fishing under
> the authority of a fishing vessel licence issued by the MMO"

i.e. **Schedule 2 DOES apply to shore anglers** — bass 42, cod 35, mackerel 20,
plaice 27, whiting 27 (cm). Schedule 1 (all fishers) gives dab 15, flounder 25,
thick-lip grey mullet 20, conger 58.

- The eel row is release-not-a-size, per gov.uk: "Any eels you catch (except conger eels)
  must be released alive" — with the conger 58 cm disambiguation clause.
- Red mullet 15 cm disambiguates the mullet row. Both clauses were sourced in the same
  build; the notes column carries nothing unsourced.
- The bass row is numerically identical to the PLAN regs box (42 cm · 3/day ·
  C&R 1 Feb–31 Mar), machine-checked.
- **The weever row is never-keep / never-handle — a safety stance, not a statute.**
  Keep that distinction if editing.

### Date stamps
Sizes say "verified … 28 Jul 2026"; the page banner stayed "reviewed 26 Jul". The whole
page was NOT re-reviewed, and bumping it would have been a false stamp.

Welsh Dee = one NRW pointer sentence, no table.
