# NW Shore Fishing Guide — Project Brief (handoff)

## What this is
A beginners' interactive guide to shore/estuary fishing in NW England (Mersey, Dee, Duddon).
Written for strangers, not for its author — the de-Craig pass shipped, so no copy is
owner-relative or brand-relative. Stated direction (2026-07-28): grow into a UK-wide
fishing-trip research workhorse — venue expansion is a future item, but new features
must not assume the current eight marks are the ceiling.
Current state: one self-contained file, `fishing-field-guide.html`, no build step.

## Non-negotiable design principles (do not regress these)
1. **Words first.** It's a guide with smart bits, not an app. Fully readable with zero
   controls touched. Progressive disclosure for every feature; defaults always sensible.
2. **Evidence honesty.** Three badges — verified / anecdotal / shifting-danger — on venues,
   sub-spots, and claims. Never flatten these. Claude-synthesised estimates (season calendar
   rows) must be distinguishable from researched facts (the "estimate" tag). "Estimate" is a
   PROVENANCE tag — who authored the number — and is orthogonal to the verified/anecdotal/
   shifting ATTESTATION axis. Never collapse the two: a fourth attestation badge is a
   flattening. While every calendar row is synthesised the tag sits on the table once, with
   the legend naming the venue-sourced windows; move it per-row the day a row gets its own
   source. Date stamps obey the same rule: "reviewed <date>" means someone read the page that day;
   "verified against <source> <date>" is only for facts actually re-checked against that
   source. Never write "checked" over facts nobody rang, sourced or measured — a false
   stamp is worse than no stamp. Bump dates only when the re-check really happened.
   Season calendars are calibrated estimates unless venue-sourced; say so. Pin coordinates
   are stance-level approximations, not surveyed fixes — gutters and channels shift
   (Duddon, Dee), so satellite plus a low-water walk beats any pin. Regs are reviewed
   annually and the page must show when it was last checked.
3. **Safety is proportionate and prominent.** Duddon/Dee sands cut-off, Perch Rock causeway,
   Rock Channel is a red DO-NOT pin (never a destination), weever first aid (hot water
   40–45°C 30+ min), shock leader mandatory above 57g/2oz casting weight.
4. **The "why" travels with every number.** Derived values (max lead = rod max − 12g rig
   allowance; hooklength = main line − ~25% weak-link rule) always show their reasoning.
5. **One knot (uni), one rig (running ledger) pedagogy.** Do not add knots/rigs to the
   teaching path; alternatives live in reference sections only.
6. **Offline-first.** Everything except map tiles works with no connection. Single-file
   preferred; if splitting becomes necessary, keep an offline-capable build output.

## Current architecture
Single HTML file, vanilla JS, **no framework** — read the file for structure, data
shapes and preset counts. Only the non-obvious constraints live here:
- The hash is written **only after first user interaction**, so a clean URL stays clean.
  Precedence hash > `efg-profile` > neutral defaults.
- Single-sourced pairs, deliberately, so the two can never drift: LEADS ↔ catalogue,
  `REELBAND`/`leaderLb()` ↔ rules ↔ gap list, `SEGS`/`VWORD` ↔ segment + row aria-labels,
  canonical `<link>` ↔ share base URL, `clsOf()` ↔ `rodOf()` thresholds.
  Never fork one side of any of these.

## Research corrections already baked in (do not revert)
- Hodbarrow Seawall is the PRIMARY Duddon mark: first of flood ~3.5h before HW, tides
  UNDER 8.4m Barrow scale, Apr–Sept, lure bass May–Sept. (Sea Angler / Alan Darby, Gint's.)
- Perch Rock is a LOW-water venue; Town Hall Steps is the HW alternative; mid-tide needs
  5–6oz (beachcaster). Otterspool is a WINTER venue (codling/whiting late Sept–Apr; tides
  over ~8.5m hard work; never park on the prom — clamping).
- Pickerings Pasture: anecdotal, nature-reserve etiquette (shore path only, never Hale
  Marsh); West Bank/Spike Island added as better-documented upper-Mersey alternative.
  Green Road was dropped (unverifiable). Askam Pier stances are anecdotal (eels left,
  flatfish right among moorings).
- 2026 regs: bass 42cm min, 3/day, catch-and-release only 1 Feb–31 Mar; Dee = statutory
  Bass Nursery Area 31 May–1 Oct (shore exempt, voluntary release expected); Duddon is NOT
  a BNA (proposed only); eels always released; EA licence only for targeting migratory fish.

## Roadmap
①–④ complete. ⑤ complete except the last item.
**Remaining: print stylesheet ("tonight's session" one-pager).**
Per-item history, decisions and errata: `specs/*.md` + `git log`.

**Convention:** post-build decisions and errata go in `specs/<item>.md` under
`## Decisions & errata (post-build)` — **not here.** This file keeps only forward
constraints. The older `## On pass (same commit) — CLAUDE.md records: …` lines in
`specs/*.md` predate this and are superseded; do not follow them.

## Invariants from shipped work — reintroducing any of these is a regression
Each of these is a trap the code exhibits but does not explain.
- **`kind`, not `type`,** is the item-kind key — `type` belongs to `line{type,…}`.
- **A shock leader must be MONO.** `type==='mono' && lb>=need`, fails closed on an
  untyped item. 50lb braid is strong enough and useless — absorbing a cast means
  stretching. Never simplify to a strength check.
- **A beachcaster at its derived 142g max on the default 15lb line is correctly RED.**
  That is the safety rule working. Do not "fix" defaults to be all-green.
  Shock-leader maths rounds to nominal ounces (142g = 5oz = 50lb); `ceil()` is wrong.
- **`SEGWIRED` guards the static SVG segments only.** Chain rows are rebuilt whole and
  their listeners die with the nodes — do NOT put them behind it.
- **Any handler that changes `state.rod` or `state.line` must end
  `defaultRig();buildRig();`** Venue buttons are exempt — `select()` calls `paintRig()`.
- **`band` is UI grouping only. No rule reads it**; classes come only from `clsOf()`.
  `GAPREEL` is separate from `REELBAND` on purpose: rules stay tolerant of owned gear,
  recommendations stay the middle of the pairing.
- **The gap list's rig line is hardcoded to the heavy running ledger** (one-rig
  pedagogy). Do not route it through a generic finder. Pulley/flappers are
  catalogue-only. The 8oz lead can never be ADVISED — it exists so owned gear is judged.
- **Decision A — silent beats invented.** RUN carries a figure only where a source
  gives one (today New Brighton alone). No `facing` field exists: OSM normals were
  wrong at New Brighton and West Bank, and a wrong gloss says "offshore" into an
  onshore blow. Any retry verifies those two against imagery first.
- **Sun + weather times are UTC internally, local only at format time.** `sunCalc`
  returns UTC minutes; Open-Meteo is requested `timezone=UTC` and sliced in UTC.
  Getting this wrong shifts the daylight window an hour all summer and looks plausible.
  Regression anchors (UTC, civil/rise/set/civil), verified against USNO — **not**
  sunrise-sunset.org, which carries a 2–3 min bias:
  Hodbarrow 15 Jan 2026 = 07:43 08:25 16:21 17:03 · 15 Jul = 03:08 03:59 20:38 21:29
  New Brighton 15 Jan = 07:39 08:20 16:24 17:04 · 15 Jul = 03:14 04:02 20:33 21:22
  `SUNDATE` and `WXGEN` are plain vars — deliberately never persisted.
- **The weather check judges comfort, never safety.** Vocabulary is exactly
  kind / workable / hard work — never "safe" or "go". Weather is the comfort axis,
  tides are the safety axis. Fetch is button-only: zero requests on page load.
- **Fail closed on regulatory and medical numbers.** No remembered value ever ships:
  transcribe from raw source HTML at build time. **Summarisers are not a source** — a
  fetch-model inverted the Schedule 2 heading. The hook-in-skin card teaches no removal
  technique for a buried barb, and stamps are scoped to what was actually re-checked.
- **A catch never changes a band symbol.** n=1 argues with a calibrated estimate in
  neither direction. `validEntry` rejects `'all'` as an entry venue.
- **A share is a READ** — no hash write, no `saveProfile()`. The link carries the setup
  hash only; a catch log must never travel silently in a pasted URL. "Copied ✓" only
  when the clipboard promise resolves.
- **`#firstvisit` is a second runtime `.deck` element.** A static grep for
  `class="deck"` undercounts.

## Explicitly rejected (do not add)
- Live tide APIs (no reliable free source; wrong tide data is worse than a link),
  price/availability data, brand product databases, embedded hardcoded videos (YouTube
  search links age better), more teaching knots, species beyond this coast,
  localStorage-only persistence without export (data loss).
