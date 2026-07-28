# Spec — ⑤ catch log

## Goal
A per-session diary that overlays the user's own catches on the season calendar.
Closed `details.ref` "My catch log" inside `#seasons`, directly under `#calwrap`, so the
diary sits next to the table it annotates. Locker patterns reused throughout: localStorage
`efg-log`, `validEntry` filter on load, JSON export/import with visible rejection of bad
files, escaped labels, add + delete (no edit-in-place).

## Provenance decision (decided now, not mid-build)
A logged catch is a THIRD class of data: **user-authored fact**. It renders as an overlay
marker in the calendar cell and **never changes the band symbol** — one afternoon (n=1)
neither upgrades "unlikely" nor argues with a sourced window. Two layers, never merged.
The overlay shows **catches only**: it answers "have I caught this, here, in this month?"
— a memory aid, not evidence. Blank sessions (no species) stay in the diary list and never
overlay; the legend line says so: "▴ you've caught this here (from your log) — a record of
your sessions, not a correction to the bands."

## Entry schema
`{date:'YYYY-MM-DD', venue:<VENUES key>|'other', species:string, notes:string}` —
empty species = blank session. Native `input type="date"` defaulting to today; venue
`<select>` from VENUES (minus 'all') + "somewhere else"; species free text with a
`<datalist>` of the union of calendar row names; notes plain text. Month for the overlay
comes from `date.slice(5,7)` — no `Date()` parsing, no timezone games.

## Overlay
`renderCal(key)` additionally marks cell (row, month) when a log entry matches:
venue = key (or ANY venue when key is 'all' — same meaning as the all-venues table),
month from the date, species matched **case-insensitively against the row name with any
parenthetical stripped** — so logged "eel" hits the "Eel (release)" row. Unmatched
species and venue 'other' live in the list only; no error, no marker. Marker is a small
`▴` via a CSS class (night mode restyles by class, not inline colour — this is HTML, not
the SVG stroke case). Band symbols and cell classes byte-identical to today.

## List UI
Newest first, flat: date · venue name · species (or "blank") · notes · delete. No stats,
no counts, no "your best month" — recomputing bands from a handful of sessions is exactly
the flattening the estimate tag exists to prevent.

## Acceptance criteria
- Empty log → `renderCal` output byte-identical to today, at every venue.
- Bass logged at Hodbarrow in July → marker on Bass/Jul at hodbarrow AND at 'all';
  band symbols unchanged everywhere.
- "eel" matches "Eel (release)"; "conger" (no row) → list only, no marker, no error.
- Blank session → list shows it, never overlays. Venue 'other' → same.
- Export → import round-trips exactly; garbage import → visible rejection, log untouched.
- Notes/species escaped on render (locker XSS precedent).
- Chip sweep writes nothing; log writes only on add/delete/import.
- JS-off: static details shell with explanatory copy, empty UI div (locker precedent).
  Offline: fully works. Night: marker legible red-on-black. Print: opens via existing
  beforeprint hook, overlay prints as rendered.

## Out of scope
Photos · weather/tide capture ("should I go" is its own ⑤ item) · size-limit checks (own
⑤ item) · edit-in-place · sharing the log via URL hash (private + oversized; the export
file is the transfer) · overlaying blanks · any calendar band change from user data.

## On pass (same commit)
CLAUDE.md: tick ⑤ catch log; record the third-provenance-class decision (overlay never
mutates bands, catches-only), the parenthetical-stripping species match, and 'all' =
any-venue scoping.
