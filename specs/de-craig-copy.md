# Spec — ①(b) De-Craig the copy

## Goal
No copy assumes the reader is Craig: no Runcorn-relative directions, no "owned" gear
statuses, no hardcoded RR numbers in prose, dog content behind a toggle. RR presets stay
in the `RODS` list (decided in ①a). All in `fishing-field-guide.html`.

## Changes

**1. `<title>` (line 6)** → `The Estuary Field Guide — beginner shore fishing on the
Mersey, Dee & Duddon`. (Also what search/share cards show.)

**2. Kit table (lines 352–356)** — header small text "✔ = already in the combo box"
→ "Grams, not just ounces." The ✔/"owned" row becomes item **0**:
`0 · Braid 15 lb main line (skip if already spooled) · Feel + distance; the nervous
system · 0.12 mm, 150 m+ · 10–15`. Drops the brand name ("Infinite") per the
spec-not-brand principle.

**3. Glossary "Casting weight" (line 756)** — "(yours: 5–40 g)" is Craig's RR rod.
Make it live: span with an id, one line in `renderGear()` sets "(yours: X–Y g)" from the
selected rod. Static HTML ships the neutral default "(yours: 10–30 g)" so the no-JS view
matches the neutral defaults. Keeps the "why travels with the number" principle.

**4. West Bank stats (line 935)** — drop `['Drive','~10 min from Runcorn']`. Parking
stat already covers access; every other venue manages without a drive time.

**5. Fish-handling line (line 568)** — "Your rod bag has a printed measure." →
"Many rod bags have a printed measure on the side — otherwise pack a tape: know what
42 cm looks like before you go." (Keeps the size-check nudge, drops Craig's bag.)

**6. Dog box → toggle (lines 669–678, roadmap's own decision)** — `.dogbox` div becomes
`<details class="dogbox">`, `<summary>` wrapping the existing `<h2>🐾 Fishing with a
dog?</h2>`, closed by default. List content byte-identical (safety content untouched —
only disclosure changes, per the roadmap). Minimal CSS: summary cursor + open/closed
indicator matching `details.ref`; keep the `.dogbox` class so night-mode rules still hit.
Checklist item (line 391) → "Dog coming? Lead, water bowl, paw-check plan" so it reads
as conditional. Print: give it the same `@media print` treatment as `details.ref`
(known pre-existing gap that closed details print collapsed — separate fix, not this item).

## Acceptance criteria
- `grep -ci craig` and `grep -ci runcorn` on the file both return 0.
- Kit table has no "owned"/✔ status; braid row is item 0 with spec + price.
- Glossary casting-weight range tracks the selected rod; shows 10–30 g with JS off.
- Dog box renders as a closed toggle, opens natively with JS off, night mode legible,
  content list unchanged; checklist dog line reads conditional.
- No venue data, badges, safety copy, or derived-number "why" notes changed beyond the
  five edits above.

## Regression risks
- Dogbox night styles (`body.night .dogbox`, lines 186–187) must still apply — keep class.
- `renderGear()` edit must not disturb specstrip/buygap output.
- Kit-table renumbering: rows 1–14 keep their numbers (row 0 inserted, none shifted).

## Out of scope
"checked <date>" stamps / verify links / date banner (①c) · estimate/approximate tags
(①d) · start-here router + credits (②) · fixing the global print-closed-details bug ·
any RODS/LINES data changes · kit inventory logic (④).
