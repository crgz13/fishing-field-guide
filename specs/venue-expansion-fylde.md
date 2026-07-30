# Spec — venue expansion ①: the Fylde coast, and the pipeline that adds a region

## Why
Stated direction (2026-07-28, the user's words): grow into a UK-wide fishing-trip
research workhorse. This item adds the first new region AND establishes the pipeline —
the contract a venue must satisfy to ship — so every later region is a repeat, not a
rethink.

## Decision 0 — why Fylde, not Wales (made now, at spec time)
The guide's regulatory spine is NWIFCA: the size-limits table is transcribed from
NWIFCA byelaws and the regs panel names NWIFCA as the authority. **NWIFCA's district
covers Cumbria, Lancashire and Merseyside — so the Fylde coast (Fleetwood, the Wyre,
Blackpool) keeps every existing regulatory claim true for the new marks.** The Welsh
side of the Dee is the geographically smaller hop but sits under Welsh
Government/NRW jurisdiction, where the NWIFCA table is simply WRONG — Wales is a
later item with its own regulatory sourcing. Fylde also extends the guide's actual
character: estuary/surf light-gear fishing with the same species set and the same
sand-and-tide danger profile the safety content already teaches.

## The venue contract (the pipeline — every mark, this region and all future ones)
A mark ships ONLY with all of:
1. **At least one named documented source** (Sea Angler venue guide, tackle-shop
   guide, or equivalent) fetched raw at build time — or it ships `anec` with the
   forum/word-of-mouth source named, like Askam. A region ships only if it has at
   least ONE `ver` mark.
2. `stats` rows each traceable to the source (ground, tide window, tide ceiling
   where sourced, season, parking, **tide-table scale** — the scale is safety-adjacent
   and must be venue-sourced, never guessed from geography).
3. A `hazard` line — sourced or clearly local-knowledge-flagged; never softened.
4. `fit` verdicts for all four rod classes with a why each (the ④(b) discipline).
5. Species month-bands as calibrated estimates under the existing table-level
   `estimate` tag; venue-sourced windows override synthesis where the source names
   one (the Hodbarrow rule).
6. Coordinates as stance-level approximations (existing pin caveat covers them).
7. `RUN` figure only if a source gives one — Decision A applies to every new mark.
8. Shops with phone numbers where findable from the source trail.

**Fail-closed gate:** if build-time research yields fewer than 2 marks meeting the
contract, STOP and report — a thin region does not ship to make a quota.

## Change
1. **2–4 Fylde marks** in `VENUES` under a new `region:'F'`, candidates to be
   confirmed against sources at build time (Fleetwood/Wyre and Blackpool-front marks
   are the expected pool — but the SOURCES decide, not this spec).
2. **Region plumbing:** third mapjump button ("Fylde marks" wording to match the
   existing two); map `fitBounds` re-checked with the wider spread; first-visit
   "Where will you fish?" gains a Fylde option targeting the region's primary mark.
3. **Chips stay one flat list.** At ~12 chips flat is still scannable; the ceiling is
   noted (group-by-region when the list passes ~14 — a future item, not this one).
4. **No new species cards unless a source demands one.** If a shipped Fylde source
   names a target species the fish-ID section lacks, it gets a card under the
   existing evidence rules; if the species set is covered (flounder/bass/whiting/
   codling etc. expected), fish ID is untouched. The "species beyond this coast"
   rejection in CLAUDE.md is amended to "species beyond shipped venues".
5. **Regs panel:** wording "Rules that apply everywhere here" must remain true —
   Fylde is NWIFCA so no regulatory change is expected; the build re-checks the
   Dee-BNA sentence still reads correctly with a third region present (it names the
   Dee specifically — fine).
6. **Everything else is generic already** (verified against the code): sun maths is
   per-coordinate, weather is per-mark, share hash carries any venue key, session
   sheet renders any venue, calendar/log/rig verdicts are venue-keyed.

## Acceptance criteria
- Every new venue entry satisfies all 8 contract points; the build report maps each
  stat to its source line (the size-limits discipline applied to venues).
- Badges honest per mark (`ver` only with a named documented source); at least one
  `ver` mark or the region does not ship.
- Sun-time spot-check for the primary Fylde mark against USNO (the existing anchor
  discipline — two dates, four times, minute-accurate).
- Chips, map (both tile and offline-SVG fallback — the fallback lists marks BY HAND
  and must gain the new region), first-visit row, mapjump: all show the new region;
  hash round-trip works with a new venue key; session sheet prints one.
- All four surfaces re-verified; `validEntry`/catch-log accept the new keys
  (they key off VENUES — confirm, don't assume).
- Size increase budgeted ≤ ~12KB for 3 marks.
- No existing venue's data changes in this item.

## Out of scope
Wales / anywhere outside NWIFCA (regulatory work) · chip grouping UI · more than 4
marks · new teaching content · tide APIs (still rejected) · changing the eight
existing marks.

## On pass (same commit)
CLAUDE.md: region list gains Fylde; the venue contract becomes a forward constraint
(link to this spec); the "species beyond this coast" rejection reworded. Errata here
under the convention.

## Decisions & errata (post-build)

- **Two marks shipped, both `ver`: Gynn Wall (Turners Tackle) and Fleetwood (Turners
  Tackle).** The gate held at its minimum — exactly 2 contract-passing marks.
  **Rossall Point failed the contract as a standalone mark** (Sea Angler gives ground,
  species, season — but no tide window and no hazard) and ships instead as a `ver`
  sub-spot of Fleetwood with its own Sea Angler attribution. Drop-don't-soften, again.
- **Tide scales are transcribed from each source page's own embedded tide widget**
  (`tidetimes.co.uk/widget?name=Fleetwood` / `…=Blackpool`) — the pages themselves
  name their reference port, so nothing was inferred from geography.
- **22 build claims + 21 review claims machine-matched** against raw fetches (review
  re-fetched all three pages independently). Negatives confirmed: no BNA claim leaked
  onto Fylde, no borrowed tide ceiling, no invented shop names — neither source names
  a tackle shop, and both venue entries say so outright rather than filling the gap.
- **Sun anchors (both marks, both solstice-side dates) minute-exact vs USNO.** The
  build's July check first looked an hour out: the USNO query was tz=0 and the guide
  prints local — BST, the exact trap CLAUDE.md documents. Code right, test wrong.
- **Review fixed a false stamp:** `renderPanel` hardcoded "reviewed 26 Jul 2026" for
  every venue — a date on which the Fylde marks did not exist. Now per-venue
  (`v.rev`, default 26 Jul 2026; Fylde carries 30 Jul 2026).
- **Review moved the two entries to geographic position** (after Askam, before Perch
  Rock) so chips and the N→F→S mapjump read the same way; conservation-checked.
- **User approved at review: the title, eyebrow and footer now name Fylde** — the
  three-region wording became false the moment Fylde shipped; same class as the PLAN
  subhead amendment.
- Coordinates from the source's own address (FY7 6UW via postcodes.io) and named
  landmark (Gynn Square via OSM) — stance-level approximations as the pin caveat says.
- Gynn's lure verdict is deliberately `y`, not `g`: the source has seen big bass but
  describes bait fishing; the estimate tag covers the month bands, not the verdicts.
