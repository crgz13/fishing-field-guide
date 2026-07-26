# NW Shore Fishing Guide — Project Brief (handoff)

## What this is
A beginners' interactive guide to shore/estuary fishing in NW England (Mersey, Dee, Duddon).
Built for Craig (novice, based near Runcorn, Rigged & Ready World Traveller 5–40g travel rod,
RR3000 reel, 15lb braid) but being generalised for strangers to use.
Current state: one self-contained file, `fishing-field-guide.html` (~80KB), no build step.

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
- Single HTML file. Vanilla JS, no framework. Leaflet 1.9.4 via cdnjs; Esri World Imagery
  satellite tiles + OSM toggle; offline fallback panel if tiles/L fail.
- Structure: sticky nav → PLAN (gear presets + map + venue panel + regs + calendar + kit +
  checklist) → DO (rig diagram, phase 1/2/aftercare, drawn SVG illustrations) → KNOW
  (fish ID, safety, dog box as closed details toggle) → LOOK UP (details-collapsed
  baits/troubleshooting/glossary).
- State `{rod, line, venue, custom weights, no-rod flag}` persisted in URL hash — written only
  after first user interaction, so clean URLs stay clean. Precedence: hash > saved profile
  (localStorage `efg-profile`, written on user changes; the hash doubles as the shareable
  export) > neutral defaults (generic light spinning, braid 15lb, all venues). First-visit
  card (location + rod-or-none) shows once when neither hash nor profile exists. Night mode
  (red-on-black) via body class + CSS var overrides incl. SVG attribute selectors. Print
  opens all <details>.
- Venue data: 8 marks + "all" in a JS object — each has lat/lng, confidence badge, blurb,
  stats, hazards, sub-spots (each with own badge), per-rod-class verdicts (g/y/r + reason),
  species-by-month strings (12 chars p/m/n), shops, region N/S.
- Gear: 7 rod presets + custom, 4 line presets; derives max lead (rounded to real lead
  sizes table), lure window, hooklength lb, drag advice. "Gear gap" box appears when the
  current rod scores y/r at the selected venue and names what to buy (or how to fish it
  on the current rod's terms).

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
- Shops: Gint's Bait & Tackle Millom (01229 777832), Lowery Tackle Haverigg (01229 772338),
  Angling & Hiking Centre Barrow (01229 829661), Bonners New Brighton (0151 638 0883).

## Prioritised roadmap (agreed order)
① **Stranger-proofing:** first-visit profile (location + rod-or-none) ✅ done, de-Craig the
   copy ✅ done (neutral title, kit table de-owned/de-branded, live casting-weight gloss,
   dog box now a closed <details> toggle, no Runcorn-relative copy),
   date stamps + verify links ✅ done (global "Page reviewed <date>" banner under the
   specstrip, per-venue "Venue details reviewed <date>" line, NWIFCA + gov.uk bass-rules
   links in the regs box; the same commit fixed print not expanding closed <details>),
   estimate/approximate tags ✅ done — **① COMPLETE** (table-level "estimate" badge on the
   season calendar + legend naming which windows are venue-sourced; "every pin is an
   approximate stance marker" in the map caption — all pins, since restricting it to amber
   pins would imply the green ones are surveyed; confidence badge added to pin popups; ●/◐
   key added to the offline map SVG so the anecdotal signal survives offline).
② **Start-here router** (never fished → DO; picking a day → PLAN; at the water → LOOK UP)
   + proper credits/sources section.
③ **Hosting:** GitHub Pages, one canonical URL; raw-file sharing becomes offline fallback.
   Also stabilises localStorage vs file:// inconsistency.
④ **Gear inventory + compatibility engine:** spec-based (never brand-based). Seed catalogue
   ~40–50 generic items + add-custom by type. Item schemas: rod{min_g,max_g},
   reel{size_class}, line{type,lb,mm}, hooklength{lb}, lead{g,wired}, hook{pattern,size},
   rig{hook_size,lead_rating}, lure{g,style}, bait{name}. Bait is SESSION-level, not
   locker-level (decided). ~10 pairwise/chain rules, each returns g/y/r + reason:
   rod↔lead(+12g), rod↔reel, reel↔line capacity, line↔hooklength weak-link, line↔lead
   shock-leader rule (hard red >57g without one), hook↔bait, lead↔venue run,
   bait/lure↔venue+month via species calendar, rig↔ground snags. Verdict renders on the
   rig-chain diagram (colour per link, tap for reason). Computed gap list replaces the
   hardcoded buygap box. Persistence: localStorage + JSON export/import (always available).
⑤ **Later:** catch log (per-session diary, export/import, overlays user data on calendar),
   dawn/dusk computed offline from mark coords, "should I go" via Open-Meteo wind/waves
   (degrade to manual entry offline), size-limits table in fish ID, hook-in-skin first aid
   card, share-config button, print stylesheet ("tonight's session" one-pager).

## Explicitly rejected (do not add)
- Live tide APIs (no reliable free source; wrong tide data is worse than a link),
  price/availability data, brand product databases, embedded hardcoded videos (YouTube
  search links age better), more teaching knots, species beyond this coast,
  localStorage-only persistence without export (data loss).

## Known caveats to preserve in copy
- Pin coordinates are stance-level approximations, not surveyed fixes.
- Gutters/channels shift (Duddon, Dee): satellite + a low-water walk beats any pin.
- Season calendars are calibrated estimates unless venue-sourced; say so.
- Regs reviewed annually; the page must show when it was last checked.
