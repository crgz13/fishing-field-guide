# Spec — ⑤ dawn/dusk

## Goal
Sun times for the selected mark — civil dawn, sunrise, sunset, civil dusk — computed
offline from the mark's own coordinates. Low light is when shy species feed close in;
the guide should say when that is at *this* mark, on the day the reader is planning.

## Placement & UI
One line inside the venue panel render (`select()`, after the `.vstats` grid, before the
"Venue details reviewed" line), so it inherits everything the panel already does: JS-off
(panel is empty — static guide copy stands alone), night mode, print, aria-live. Format:

> **Sun (12 Aug):** first light ~05:14 · rise 05:52 · set 20:41 · last light ~21:19 —
> computed from this mark's coordinates (±2 min). Dawn and dusk are the feeding windows.

Native `<input type="date">` beside it, defaulting to today — PLAN is "picking a day",
and the day being planned is usually not today. The chosen date lives in a plain JS var
(NOT state/hash/localStorage) and re-applies on venue change, because `select()` rebuilds
the panel's innerHTML and would otherwise stomp the input. At venue **'all'** the line is
a pointer instead: "pick a mark for sun times" — no single mark, no single answer.

## Computation
NOAA solar algorithm, ~25 lines of vanilla JS, no dependency, pure maths — fully offline.
Civil twilight = sun 6° below horizon (the standard usable-light definition; "first/last
light" in the copy, since strangers don't know "civil"). Display always **Europe/London**
via `toLocaleTimeString(…,{timeZone:'Europe/London'})` — every mark is in England, and
the native formatter handles GMT/BST so we write zero DST code. Invalid/empty date input
falls back to today, silently.

## Provenance
"Computed" is a third kind of gloss: deterministic maths, not a synthesised estimate,
not a sourced fact — so no "estimate" tag, but the why-travels rule applies: the line
names its inputs (this mark's coordinates) and its precision (±2 min). No new badge.

## Acceptance criteria
- Times match a published almanac (timeanddate.com) for 2 marks × 2 dates (one GMT, one
  BST) within ±3 min; the build report shows the comparison, per the honest-verification
  rule — no "verified" claim without the numbers.
- Date change updates times; venue change keeps the chosen date; 'all' shows the pointer.
- No storage writes, no network requests, URL hash untouched, chip sweep writes nothing.
- Offline: identical behaviour. JS-off / night / print: inherited from the panel — confirm,
  don't assume.

## Out of scope
Tides (rejected list) · moon phase · "best time to fish" scoring (that judgment belongs
to "should I go") · persisting the chosen date · sun times in the print one-pager (that
item designs its own layout).

## On pass (same commit)
CLAUDE.md: tick dawn/dusk; record the fixed-Europe/London formatting trick, civil
twilight as "first/last light", the computed-±2min gloss as the provenance wording, and
'all' → pointer not a number.
