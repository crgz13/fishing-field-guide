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

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

Per-mark sun line in the venue panel (first light · sunrise · sunset · last light +
native date picker). NOAA algorithm inline (~30 lines, two-pass), pure maths, zero
network. Display pinned to Europe/London via `toLocaleTimeString` — every mark is in
England, the native formatter does GMT/BST, we own no DST code.

### Two spec claims were rejected during the build
1. **The spec's "±2 min" gloss was WRONG IN KIND** and was replaced (approved) by the
   flat-horizon caveat. An almanac check validates the *algorithm*, not the *sightline*:
   Black Combe behind Silecroft, or Hodbarrow's seawall, cuts real light far more than
   any ±2 min. The copy now says "against a flat horizon… high ground or a wall behind
   you cuts the light shorter". This supersedes the "On pass" line above asking for the
   computed-±2min gloss as the provenance wording.
2. **The spec's "dawn and dusk are the feeding windows" was an unbadged claim** and
   became a pointer to the existing Phase 2 lure copy instead. Do not re-add it as a
   bare assertion.

Venue `'all'` → a pointer sentence, never a number.

### Wiring
`SUNDATE` is a plain JS var — deliberately **NOT** in hash, profile or storage. One
delegated `change` listener on the static `#vpanel` (the `SEGWIRED` lesson: `renderPanel`
rebuilds innerHTML per venue) rewrites only the `#suntimes` span — a full re-render would
drop picker focus and re-announce the whole aria-live region. Print hides the picker only;
the date is spelled out in the sentence.

### Regression anchors — the only test oracle this project has
Verified against **USNO** (aa.usno.navy.mil), **not** sunrise-sunset.org, which showed a
systematic 2–3 min rise/set bias. 15/16 values exact, worst 1 min.

UTC, civil / rise / set / civil:

| mark | 15 Jan 2026 | 15 Jul 2026 |
|---|---|---|
| Hodbarrow | 07:43 08:25 16:21 17:03 | 03:08 03:59 20:38 21:29 |
| New Brighton | 07:39 08:20 16:24 17:04 | 03:14 04:02 20:33 21:22 |

The New Brighton 15 Jul last light of **21:22** corrects a transcription error in the
original anchor (raw value 1281.53 min rounds to 21:22). `sunCalc` itself was and is
right; only the recorded line was off by one.
