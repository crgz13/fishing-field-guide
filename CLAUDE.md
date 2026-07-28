# NW Shore Fishing Guide — Project Brief (handoff)

## What this is
A beginners' interactive guide to shore/estuary fishing in NW England (Mersey, Dee, Duddon).
Built for Craig (novice, based near Runcorn, Rigged & Ready World Traveller 5–40g travel rod,
RR3000 reel, 15lb braid) but being generalised for strangers to use. Stated direction
(2026-07-28): grow into a UK-wide fishing-trip research workhorse — venue expansion is a
future item, but new features should not assume the current eight marks are the ceiling.
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
② **Start-here router** + credits/sources ✅ done — three static anchors in the header
   (never fished → DO; picking a day → PLAN; at the water → LOOK UP), zero JS, print-hidden;
   "Sources & credits" lives as a details.ref at the end of LOOK UP (reference material
   belongs in reference sections), naming Claude as synthesiser of the month bands and
   crediting the venue research as other people's work; footer points at it.
③ **Hosting** ✅ done — GitHub Pages live at https://crgz13.github.io/fishing-field-guide/
   (index redirect + README since baseline; localStorage stabilised by the hosted URL).
   Closed by a canonical <link> in the head and a footer pointer so the raw file — now the
   offline fallback — names the live URL as visible text that survives print and offline.
④ **Gear inventory + compatibility engine:** spec-based (never brand-based). Seed catalogue
   ~40–50 generic items + add-custom by type. Item schemas: rod{min_g,max_g},
   reel{size_class}, line{type,lb,mm}, hooklength{lb}, lead{g,wired}, hook{pattern,size},
   rig{hook_size,lead_rating}, lure{g,style}, bait{name}. Bait is SESSION-level, not
   locker-level (decided) — now stated in the locker's own copy, not just here.
   Split into three cycles: **④(a) locker ✅ done** — schemas + 51-item generic catalogue
   (leads generated from the LEADS table, so the two can't drift) + "My gear locker" as a
   closed details in PLAN + localStorage `efg-locker` + native JSON export/import with
   visible rejection of bad files. Stores only; no rules. Single file kept (91→105KB).
   NOTE: the item-kind key is `kind`, NOT `type` — `type` belongs to line{type,…} and the
   two collided. ④(b) rules + rig-chain verdicts — needs per-venue run/ground data that
   does not exist yet — resolved in **④(b) ✅ done**: GROUND is a re-labelling of each
   venue's existing sourced "Ground" stat (snaggy/mixed/clean/soft, no new claims, badge
   inherited); RUN carries a figure ONLY where a source gives one — today New Brighton
   alone ("5–6oz to hold mid-tide") — and the rule stays SILENT elsewhere rather than
   estimating a holding weight (**decision A**; a guessed holding weight is a
   safety-adjacent invention about a real tidal mark). 9 rules render as link colours on
   the rig-chain SVG, tap/keyboard for reasons; unjudged links keep their original stroke.
   NOTE: a beachcaster at its own derived 142g max lead on the default 15lb line is
   correctly RED (needs ~50lb shock leader) — that is the safety rule working, do not
   "fix" it to make defaults all-green. Shock-leader maths rounds to nominal ounces
   (142g = 5oz = 50lb); ceil() wrongly demanded 60lb.
   **④(c) ✅ done** — the hardcoded buy-lists are gone; `gapNeeds(class,venue)` computes
   rod/reel/leader/lead/rig (or lures, for a lure target), checking the LOCKER first
   ("✓ in your locker") then naming a generic catalogue spec ("to add"); all-owned flips
   the headline. Decisions: **£ price hints dropped** (price/availability is on the
   rejected list — the shops line is where that belongs) and the old box's **flapper
   suggestion replaced by the heavy running ledger** (recommending a flapper contradicted
   the one-rig pedagogy — computing the list surfaced it). Rod rest dropped: not
   representable spec-based. `REELBAND` + `leaderLb()` are shared by the rules and the gap
   list so the two can't drift (the LEADS/catalogue trick again). Locker rods are classed
   by `clsOf()`, the same thresholds `rodOf()` uses for custom rods. The lure↔month verdict
   (deferred from ④b) renders here, reading only calendar rows this guide already calls
   lure quarry ("bass"), always carrying the estimate-provenance sentence. TWO BUGS THIS
   FIXED, do not reintroduce: the old box crashed to "you'd add:undefined" whenever the
   target class was `light` (no `light` key in `buy` — reachable with a lure rod at
   Pickerings/West Bank); and an owned **shock leader must be mono** — matching on
   breaking strain alone let a 50lb braid score a green ✓ against the one rule CLAUDE.md
   calls mandatory, and braid cannot stretch. `mkNeed` is named that way because a local
   `var need` inside the shock-leader rule would shadow a global `need()`.
   Both gaps ④(c) left open are now closed. **A shock leader must be MONO** — the rule
   tests `type==='mono' && lb>=need` and fails closed on an untyped item, because a 50lb
   braid is strong enough and still useless: absorbing the cast means stretching, which
   braid cannot do. Never "simplify" this back to a strength check. `lockerChanged()`
   now calls `buildRig()` too, so locker gear reaches the rig selects without a reload;
   that required wiring the SVG segment listeners **once** behind `SEGWIRED`, since
   `buildRig()` re-runs on every rod/line/locker change and was stacking a fresh
   click/keydown pair each time (four handler calls per click after three rebuilds).
   ~10 pairwise/chain rules, each returns g/y/r + reason:
   rod↔lead(+12g), rod↔reel, reel↔line capacity, line↔hooklength weak-link, line↔lead
   shock-leader rule (hard red >57g without one), hook↔bait, lead↔venue run,
   bait/lure↔venue+month via species calendar, rig↔ground snags. Verdict renders on the
   rig-chain diagram (colour per link, tap for reason). Computed gap list replaces the
   hardcoded buygap box. Persistence: localStorage + JSON export/import (always available).
   **④(a)+ catalogue range ✅ done** — 51→76 items spanning ultralight/LRF (1–10g rod,
   2000 reel, 8lb braid, 4/6lb fluoro, hooks 8/6, 2–5g lures) through the 60–100g hole
   (2–4oz rough-ground rod + the 40lb shock leader that chain was missing) to an 8oz
   heavy tier (150–227g rod, 10000 reel, 80lb mono shock, pulley rig, 8oz leads).
   Justified by the stated direction: the page is meant to become a UK-wide trip-research
   workhorse, so the catalogue is not capped by the current eight marks' needs. Decisions:
   `band` is a UI grouping (native <optgroup>, smallest→biggest) and NO rule reads it —
   classes still come only from `clsOf()`; REELBAND light AND lure widened 2500→2000
   (identical twins, same rationale) and beach 8000→10000, with a `ponytail:` comment
   naming the accepted ceiling (a true 1000-size LRF reel scores amber — the fix is a
   fifth rod class costing 8 new venue claims; wait for ultralight venue data). `GAPREEL`
   exists because widening the band would otherwise have changed BUY advice (fromCat's
   first-in-band would recommend a 2000 to light-rod users): rules stay tolerant of owned
   gear, recommendations stay the middle of the pairing — parity with pre-change advice
   machine-verified. The pulley rig is catalogue-only like the flappers: the gap list's
   rig line is hardcoded to the heavy running ledger (one-rig pedagogy), do not route it
   through a generic finder. LEADS gained 7g (0.25oz — a 5–20g rod now derives 7g instead
   of "—") and 227g (8oz); the 8oz lead can never be ADVISED (needs a 239g+ rod; even the
   227g rod derives 170g) — it exists so owned gear gets judged, not to be recommended.
   Spec errata recorded honestly: the spec's "heavy rod + 4000 reel = red" acceptance
   criterion was wrong in fact (it is and was amber — the beach band's floor never moved).
⑤ Seven independent items; **catch log ✅ done**. A logged catch is a THIRD provenance
   class — user-authored fact, beside the synthesised bands and the sourced windows — and
   renders as a ▴ overlay on the calendar that **never changes a band symbol**: n=1 argues
   with a calibrated estimate in neither direction, so the overlay is catches-only and
   blanks/unlisted-venue sessions list in the diary but never mark the table (the copy says
   why, both directions). Species match strips parentheticals ("eel" hits "Eel (release)");
   venue 'all' on the table means caught-at-any-mark, and `validEntry` REJECTS 'all' as an
   entry venue (an imported 'all' would mark every mark at once) and fails closed on bad
   dates/unknown marks. The ▴ key in the legend stays hidden until a marker exists. The
   details sits after the legend paragraph, not directly under the table — the legend's
   -18px top margin overlaps the table. The log form re-defaults date/mark to the venue
   being viewed on details-open, guarded on species+notes being empty so it can't stomp a
   half-filled form. localStorage `efg-log` + JSON export/import, locker patterns verbatim.
   No stats/"your best month" — recomputing bands from a handful of sessions is the exact
   flattening the estimate tag prevents.
   **dawn/dusk ✅ done** — per-mark sun line in the venue panel (first light · sunrise ·
   sunset · last light + native date picker), NOAA algorithm inline (~30 lines, two-pass),
   pure maths, zero network. Display is pinned to Europe/London via `toLocaleTimeString`
   — every mark is in England, the native formatter does GMT/BST, we own no DST code.
   Decisions: the spec's "±2 min" gloss was WRONG IN KIND and was replaced (approved) by
   the flat-horizon caveat — an almanac check validates the algorithm, not the sightline;
   Black Combe behind Silecroft or Hodbarrow's seawall cuts real light far more than any
   ±2 min, so the copy says "against a flat horizon… high ground or a wall behind you
   cuts the light shorter". The spec's "dawn and dusk are the feeding windows" was an
   unbadged claim and became a pointer to the existing Phase 2 lure copy instead — do not
   re-add it as a bare assertion. Venue 'all' → a pointer sentence, never a number.
   `SUNDATE` is a plain JS var — deliberately NOT in hash/profile/storage. One delegated
   change listener on the static `#vpanel` (the SEGWIRED lesson: renderPanel rebuilds
   innerHTML per venue) rewrites only the `#suntimes` span — a full re-render would drop
   picker focus and re-announce the whole aria-live region. Print hides the picker only;
   the date is spelled out in the sentence. Verified against USNO (aa.usno.navy.mil),
   NOT sunrise-sunset.org, which showed a systematic 2–3 min rise/set bias; 15/16 values
   exact, worst 1 min. Regression anchors (UTC, civil/rise/set/civil): Hodbarrow 15 Jan
   2026 = 07:43 08:25 16:21 17:03 · 15 Jul = 03:08 03:59 20:38 21:29; New Brighton
   15 Jan = 07:39 08:20 16:24 17:04 · 15 Jul = 03:14 04:02 20:33 21:22.
   (21:22 corrects a transcription error in the original anchor: raw value 1281.53 min
   rounds to 21:22 — sunCalc itself was and is right; only this line was off by one.)
   **"should I go" / weather check ✅ done** — closed details.ref in PLAN after #vpanel.
   **It judges comfort, never safety**: verdict vocabulary is exactly kind / workable /
   hard work (never "safe"/"go"), and the mandatory tide sentence — weather is the
   comfort axis, tides are the safety axis, no live tide data on purpose — is static HTML
   beside a static band table (<10 / 10–20 / >20 mph, gusts >28) carrying the
   "rule-of-thumb bands, synthesised" provenance line, all readable JS-off. **Fetch is
   button-only**: zero requests on page load and a full chip sweep fires none; Open-Meteo
   (first live API beyond tiles, approved 28 Jul 2026), coords-in-URL is fine (public
   venue data, not personal). Manual entry is always present and shares `wxBand()` with
   the fetched path so offline can't drift. Forecast requested with `timezone=UTC` and
   sliced in UTC because `sunCalc` returns UTC minutes — format-only-local; getting this
   wrong shifts the daylight window an hour all summer while looking plausible. Banded on
   the windiest daylight hour (the copy says so). Waves are a fact judged by no rule,
   absent where the marine grid is null (West Bank + Pickerings verified all-null).
   `WXGEN` generation counter stops late wave responses landing under a newer verdict;
   `wxClear()` in `select()` drops a verdict on venue change (fetched for one mark, does
   not travel) and the fetched line names its mark. Out-of-range date (HTTP 400) gets its
   own message — Open-Meteo reaches ~a fortnight.
   **APPROVED DEVIATION — onshore/offshore gloss dropped.** OSM coastline normals were
   computed for all 8 marks (Overpass, seaward = tangent+90°): right at 6, WRONG at
   New Brighton (101° E — promontory corner; truth ≈ N/NW) and West Bank (358° N — the
   north bank must face S). A wrong gloss says "offshore" into an onshore blow, so no
   `facing` field exists and wind direction renders as a plain fact ("6–17 mph WSW") —
   Decision A again: silent beats invented. Any future attempt must verify bearings
   per-mark against imagery, the two known-bad ones first.
   **size-limits table ✅ done** — static "Can I keep it?" table in fish ID (#fishid),
   visible not collapsed (a legal minimum behind a toggle is the wrong disclosure level),
   zero JS/storage/network. **The fail-closed rule for regulatory numbers**: no
   remembered value ever ships — every size was transcribed at build time from raw
   NWIFCA/gov.uk HTML (summarisers are NOT a source: the review caught WebFetch's model
   inverting the Schedule 2 heading), and the review re-fetched and re-matched every
   number independently. Verbatim: "Schedule 2 also applies to all persons fishing in
   the district, unless fishing under the authority of a fishing vessel licence issued
   by the MMO" — i.e. Schedule 2 DOES apply to shore anglers (bass 42, cod 35, mackerel
   20, plaice 27, whiting 27); Schedule 1 (all fishers) gives dab 15, flounder 25,
   thick-lip grey mullet 20, conger 58. Eel row is release-not-a-size (gov.uk: "Any
   eels you catch (except conger eels) must be released alive"), with the conger 58 cm
   disambiguation clause; red mullet 15 cm disambiguates the mullet row — both clauses
   sourced in the same build, the notes column carries nothing unsourced. Bass row is
   numerically identical to the PLAN regs box (42 cm · 3/day · C&R 1 Feb–31 Mar),
   machine-checked. Weever row = never keep/never handle (safety stance, not a statute —
   keep that distinction if editing). Date stamps: sizes say "verified … 28 Jul 2026",
   the page banner stays "reviewed 26 Jul" — the whole page was NOT re-reviewed, and
   bumping it would be a false stamp. Welsh Dee = one NRW pointer sentence, no table.
   **rig-chain presentation ✅ done** — the ④(b) checker was a flex row of six unordered
   dropdowns that LOST their slot name once filled (a default page showed three unlabelled
   boxes), started mid-air at the reel, and painted verdicts only on the diagram, which
   scrolls away from the controls on a phone. Now a vertical `.chain` list in the order the
   tackle runs: **Rod → Reel → Main line → Shock leader → Lead → Hooklength → Hook → Bait**,
   each row `label · control · verdict dot`, tap for the same why the diagram taps give.
   Decisions: **CSS grid, not flex** (`120px minmax(0,1fr)`) — a long read-only value wraps
   inside its own column instead of dropping under the label and breaking alignment down the
   chain; **rod + main line are read-only rows** fed from the PLAN setup, and they carry NO
   verdict of their own — what they are PAIRED with is what gets judged, so their dots stay
   hollow and `ENDWHY` points at where the verdict actually lives (this replaced main line's
   old "nothing to judge here yet", which was false over a link that plainly matters);
   **the shock-leader rule gained a second slot** (`slots:['lead','leader']`) so the verdict
   also shows where the user sets the leader — same verdict in two places, NOT a new
   judgement, and `paintRig()` still iterates only the six `seg-` ids so the SVG is
   untouched. The diagram gained a **rod butt** left of the reel (viewBox `0 0 900 240` →
   `-120 0 1020 240`, water path extended to -120 or it would stop short); its sub-label is
   the ONLY dynamic text in the diagram, which is why the intro now says "only the rod name
   under it is yours". Option labels de-duped ("Aberdeen size 4", not "Aberdeen — Aberdeen
   size 4"). `SLOTS` is gone, replaced by `CHAIN` (third field 1 = editable here, 0 = from
   PLAN); `SEGS` and `VWORD` are shared so the segment and row aria-labels can't drift.
   The chain rows are rebuilt whole on every change, so their listeners die with the nodes —
   **do NOT put them behind SEGWIRED**, that guard is only for the static SVG segments.
   Verified: 64 assertions incl. 6 rod classes × 9 venues with row dots and segment strokes
   agreeing, and the mono-only leader rule re-checked 4 ways (bare/mono50/braid80/untyped).
   A green dot on an unset Shock leader under 57g is CORRECT — the rule's own words are
   "no shock leader needed", and green means ok.
   **Remaining ⑤ (any order):** hook-in-skin first aid card, share-config button,
   print stylesheet ("tonight's session" one-pager).

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
