# Spec — ② start-here router + credits/sources section

## Goal
A stranger landing on the page is routed by **situation**, not section name; and every
piece of research behind the page is credited in one findable place. All in
`fishing-field-guide.html`. No data values, verdicts or safety copy change.

## Survey findings the design rests on (grep-verified)
- All three targets are **static anchors already in the HTML**: `#do` (line 405),
  `#plan` (303), `#lookup` (694). The router needs **zero JS** — it is three `<a>` tags.
- The sticky nav (line 282) lists *section names*; it cannot answer "which one am I?".
  The router translates situation → section, which is the one job the nav can't do.
- The first-visit card inserts inside `#plan` before the gear deck (line 1119) and is
  one-time; the router is static and permanent. No coupling, no conflict — a
  "picking a day" click simply lands on the card when it's showing.
- Sources are scattered across: VENUES blurbs (Sea Angler / Alan Darby, Turners,
  Taskers, WSF/forums, four tackle shops), the regs box (NWIFCA + gov.uk links,
  line 349), the footer one-liner (773), and in-map tile attribution (Esri line 1028,
  OSM 1029). Nothing consolidates them.
- LOOK UP is built of `<details class="ref">` blocks that all open on print —
  the established home for reference material (principle #5's "alternatives live in
  reference sections" logic applies to credits too).

## Changes

**1. Router — static HTML in the header** after the `.checked` line (297), inside
`.wrap`. Three anchors, situation-first copy:
> **Never fished before?** → Start at **Do** — the rig, the one knot, the first cast, in order.
> **Picking a day to go?** → Start at **Plan** — gear, mark, tides, what's biting.
> **At the water right now?** → Straight to **Look up** — baits, troubleshooting, what have I caught.

Markup: `<div class="router">` with three `<a href="#do|#plan|#lookup">`. One new CSS
block (`.router`, `.router a`, a night-mode rule if header night styles don't already
cover link colour — check at build). `@media print{.router{display:none}}` — navigation
is dead weight on paper, same treatment as the first-visit card (line 233).

**2. Credits — one new `<details class="ref">` "Sources &amp; credits"** at the end of
LOOK UP (before the section close, ~line 769). Content, in this order:
- **Venue research:** Sea Angler magazine (Alan Darby's Duddon pieces, via Gint's of
  Millom) · Turners Tackle &amp; Taskers Angling, Liverpool (Mersey marks) · World Sea
  Fishing and local forums (the amber-badged marks — single-source, verify locally).
- **Local knowledge:** the four tackle shops named in the venue panels — Gint's
  (Millom), Lowery (Haverigg), Angling &amp; Hiking Centre (Barrow), Bonners
  (New Brighton). *No phone numbers here — the venue panels carry them; credits credit.*
- **Regulations:** NWIFCA and gov.uk bass guidance, same two links as the regs box,
  with the existing "verified 26 Jul 2026" date restated.
- **Maps:** Leaflet · Imagery © Esri, Maxar, Earthstar Geographics · © OpenStreetMap
  contributors — mirrors the in-map attribution so it survives print/offline.
- **Season calendar &amp; synthesis:** month bands are calibrated estimates synthesised
  by Claude (an AI assistant) from the research above, not catch records — the
  "estimate" badge on the calendar marks this. Guide assembled with Claude; every
  verified/anecdotal badge says which claims stand on named research.
  **Veto point: say so if you don't want Claude named.** Evidence honesty argues for it —
  the credits section is exactly where "who authored this" belongs.

**3. Footer (773)** — unchanged except appending: ` · Full sources: see "Sources &
credits" under Look up.` The one-liner stays; it now points somewhere.

## Acceptance criteria
- Router works with JS disabled (plain anchors) and offline; hidden in print.
- Router copy uses the exact roadmap mapping: never fished → DO, picking a day → PLAN,
  at the water → LOOK UP. Three links, no more — it is not onboarding, not a wizard.
- Night mode: router links and the new details block legible (`details.ref` already in
  the night selector list — verify, don't assume).
- Credits open on print like every other `details.ref` (①(c) listener covers all
  `details` — confirm it catches the new one for free).
- No source dropped from the footer line; no phone numbers duplicated; regs links in
  credits byte-identical to the regs-box URLs.
- No badge, safety copy, venue datum or "why" note touched.

## Regression risks
- Header edit sits above the fold — check specstrip/standfirst spacing didn't shift.
- New `details.ref` must be *inside* `#lookup`'s section so print-open and night rules
  inherit; after the section close it gets neither.
- Footer is outside `.wrap`-sectioned night rules — check appended text inherits footer
  colour as-is.

## Out of scope
Reordering sections · any change to the sticky nav · a "start here" modal/wizard ·
per-venue bibliography entries in VENUES (add if a venue ever gets a formal source
field) · hosting/③.

## On pass (same commit)
CLAUDE.md: tick ② in the roadmap; note the credits-section location decision
(reference material lives in LOOK UP) if accepted as stated.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ② on 30 Jul 2026.

Shipped: three static anchors in the header (never fished → DO; picking a day → PLAN;
at the water → LOOK UP), zero JS, print-hidden.

Decision: "Sources & credits" lives as a `details.ref` at the end of LOOK UP —
reference material belongs in reference sections. It names Claude as the synthesiser of
the month bands and credits the venue research as other people's work. The footer
points at it.
