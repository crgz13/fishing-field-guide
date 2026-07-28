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
