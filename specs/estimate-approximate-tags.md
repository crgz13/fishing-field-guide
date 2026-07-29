# Spec — ①(d) "estimate" tag on calendar rows + "approximate" on pins

## Goal
Claude-synthesised season data is visibly distinguishable from researched facts, and pin
coordinates stop reading as surveyed fixes. Closes roadmap ①. All in
`fishing-field-guide.html`. **No data values change** — this item is labelling only.

**Axis rule (goes into CLAUDE.md on pass):** "estimate" is a **provenance** tag — who
authored the number. verified / anecdotal / shifting is an **attestation** axis — how well
the claim is attested. Orthogonal. Never render "estimate" as a fourth attestation badge.

## Survey findings the design rests on (grep-verified, not assumed)
- All **45** `species` rows are two-element `['Name','pmn…']`; none carries provenance.
  With zero researched-fact rows to contrast against, 45 identical per-row badges would
  distinguish nothing — the honest unit is **one table-level tag**, stated once, plus
  legend copy naming the exceptions. **Veto point: say so if you want per-row badges.**
- `renderCal()` (line 967) has no `all` early-return and `#calwrap` is empty in static
  HTML — so the tag lives in the **static heading + legend**, covering all nine views and
  surviving JS-off. The calendar half of this item touches **no JS**.
- Badges `.b-ver/.b-anec/.b-shift` (lines 247–249) have **no night override** — they keep
  day colours at night by design. A new `.b-est` in the same idiom needs no night rule.
- The offline map SVG (line 328) draws ●/◐ confidence symbols with **no key** — the
  anecdotal signal currently dies exactly in the offline case principle #6 protects.
  One `<text>` line fixes it; flagged as a drive-along, veto if you want it separate.

## Changes

**1. CSS, one rule** after `.b-shift` (line 249):
`.b-est{background:#e4e8ea;color:#3c4c56;border:1px solid #3c4c56}`
Neutral slate, deliberately off the green/amber/red attestation ramp.

**2. Calendar heading (line 352)** — after the `#calvenue` span, **before** `<small>`
(which is `display:block` — after it, the badge drops a line):
`<span class="badge b-est">estimate</span>`. JS only sets `calvenue.textContent`; a
sibling is safe.

**3. Calendar legend (line 354)** — append verbatim:
> **Every row here is an estimate.** The month bands were calibrated from each mark's own
> notes and the research credited in the venue panel — not from catch records. Where a
> venue's notes name a hard window (Hodbarrow's April–September, Silecroft's October
> codling), that window is the sourced fact and the row follows it.

**4. Map caption (line 326)** — append verbatim:
> **Every pin is an approximate stance marker, not a surveyed fix** — it shows which
> structure to walk to, not where to stand.

All pins, deliberately: "approximate" on amber pins only would imply the green pins *are*
surveyed, which is false. The roadmap line decomposes into two true statements — all pins
approximate (this change), anecdotal attestation (change 5).

**5. Pin popups (line 1034)** — reuse the existing badge, one expression:
`'<b>'+v.name+'</b> '+BADGE[v.conf]+'<br>'+…` — popup now matches the venue panel.
No second vocabulary, no new badge type.

**6. Offline SVG key** — new line after the last `<text>` (y=220; viewBox is 420×300):
`<text x="60" y="248" font-size="10" fill="#3c4c56">● verified · ◐ anecdotal — pin positions approximate</text>`

## Acceptance criteria
- `git diff` shows zero changed month strings — `species` arrays byte-identical.
- Estimate tag + legend copy visible with JS off, and on all nine calendar views incl. all.
- Night mode: `.b-est` legible on the dark ground; `.legend` already in the night selector
  list (line 192) so the new sentence inherits.
- Offline SVG shows the ●/◐ key; nothing clips (y=248 < 300).
- Popup badge text identical to the panel's `BADGE[v.conf]` output.
- No badge removed, downgraded or merged; no safety copy, venue data or "why" note touched.

## Regression risks
- `.phase` sets `font-stretch:62%` — if the heading badge renders visibly condensed
  vs badges elsewhere, add `font-stretch:100%` to `.b-est`. Check at build, don't pre-fix.
- Map caption edit is inside an existing `<p>` with inline style — append inside it, don't
  restructure.
- Print: badge + legend are static text; confirm they print (no `@media print` hides them).

## Out of scope
Start-here router + credits (②) · per-row provenance fields in `VENUES` (add when a row is
actually sourced, not before) · re-researching any month band · any venue/regs fact change.

## On pass (same commit)
CLAUDE.md: tick ①(d) — roadmap ① complete; add the provenance-vs-attestation axis rule to
principle #2.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ① on 30 Jul 2026. Closed roadmap ①.

- Table-level "estimate" badge on the season calendar, with the legend naming which
  windows are venue-sourced. The tag sits on the table **once** while every row is
  synthesised; it moves per-row the day a row gets its own source.
- "Every pin is an approximate stance marker" in the map caption — **all** pins, since
  restricting it to amber pins would imply the green ones are surveyed.
- Confidence badge added to pin popups.
- ●/◐ key added to the offline map SVG so the anecdotal signal survives offline.

The provenance-vs-attestation orthogonality rule this pass added to principle 2 is the
reason the catch log later became a *third* provenance class rather than a fourth badge.
