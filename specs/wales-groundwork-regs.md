# Spec — Wales groundwork: the regs become authority-scoped

## Why
Stated direction: UK-wide fishing-trip research workhorse. The venue contract says
new regions outside NWIFCA need their own regulatory sourcing before anything else —
this item IS that sourcing, for Wales, plus the restructure that makes regulatory
content authority-scoped instead of silently single-authority. Welsh marks are the
follow-on item; after this one they are pure venue-contract work.

## Spec-time feasibility (done 30 Jul 2026 — sources confirmed transcribable)
- **gov.wales Bass fishing: 2026 guidance** — PDF (landing page links only a PDF;
  `pdftotext` via poppler, now installed). Section 6 states: rules apply from boat
  **or shore**; C&R only 1 Feb–31 Mar; **three per day** outside it (Sea Fisheries
  (Amendment) Regulations 2026); **MCRS 42 cm**; BNAs per the Bass (Specified Areas)
  (Prohibition of Fishing) Order 1990. **Matches the shipped gov.uk numbers exactly**
  — bass is a genuine everywhere-rule once dual-sourced.
- **gov.wales MCRS of sea bass** — static HTML, 42 cm.
- **NRW Buy a fishing rod licence** — static HTML. Licence list: salmon, trout,
  freshwater fish, **smelt or eel**, in **Wales or England** (one shared regime).
  The sea exemption is stated only as this positive list — see Decision 4.
- **legislation.gov.uk Bass (Specified Areas) Order 1990** — static HTML (already
  used by the size-limits item).
- **Not yet found:** a Welsh source for non-bass recreational sizes (legacy
  NW&NW Sea Fisheries Committee byelaws, expected on gov.wales). Build searches;
  Decision 3 defines the fail-closed path.

## Decisions (made now, at spec time)
- **Decision 0 — groundwork ships no marks.** No `region:'W'`, no map/chips/first-visit
  change, no title/eyebrow/footer change (the region list stays Duddon, Fylde, Mersey
  & Dee — Wales enters it only when Welsh marks ship). This item is regulatory spine only.
- **Decision 1 — no speculative plumbing.** No `AUTH` data structure, no venue-field
  changes: nothing would consume them yet. Authority scoping is CONTENT (which
  sentence sits under which named regulator). The Welsh-marks item adds plumbing
  when a venue needs it.
- **Decision 2 — an "everywhere" claim needs every authority's source.** Bass, eels,
  licence, Dee BNA: each re-verified at build time against BOTH the English source
  (gov.uk / NWIFCA) and the Welsh source (gov.wales / NRW), raw fetches, line-by-line.
  A claim only one authority supports gets scoped to that authority instead of
  staying under "everywhere". Stamps name each source actually re-fetched, with the
  real fetch date — the existing gov.uk date bumps ONLY because the build re-fetches
  gov.uk (it must, to dual-source honestly).
- **Decision 3 — honest absence for Welsh sizes.** If build-time search finds no
  transcribable Welsh source for non-bass recreational sizes, the Wales block says
  exactly that: the only Welsh-sourced size is bass 42 cm, and the English table is
  offered as a voluntary yardstick — clearly flagged as this guide's own
  conservation-side advice, not Welsh law (consistent with "unsure = treat as
  undersized"). Never transpose NWIFCA numbers onto Wales as if they were law.
- **Decision 4 — the licence sentence must match the statutory list.** The shipped
  sentence says a licence is needed "to target salmon/sea trout"; the NRW list also
  names **eel and smelt**, and Askam documents an eel stance — under-stating this is
  a real reader risk. Build transcribes gov.uk's when-you-need-a-licence page and
  the NRW page; the sentence ships the sourced list (eels go back regardless — the
  guide already says so everywhere). If the sources differ, scope per country.

## Change
1. **Regs panel (PLAN)** — stays one visual block, one `<p>` (see 4). Restructured
   content: the everywhere-rules (bass numbers, eels, licence per Decision 4, Dee
   BNA) now dual-stamped; plus one new sentence naming who regulates where —
   English shore = NWIFCA, Welsh side of the Dee = Welsh Government/NRW — with
   verify links to both.
2. **KNOW sizes section** — the ten table rows byte-identical. The existing
   "English coast / Welsh shore is NRW water — check before you fish that side"
   caveat paragraph upgrades into a short sourced Wales block per Decision 3, with
   its own stamp scoped to what was actually fetched.
3. **Footer LOOK UP regulations line** — gains the gov.wales/NRW links alongside
   NWIFCA/gov.uk.
4. **Session sheet compatibility** — `sessionSheet()` reads `.regs p` (first `<p>`
   only) for the `data-cut="regs"` block. Keep the panel one `<p>`, or update the
   extractor in the same change — the sheet must carry the full rules text either way.
5. **Dee BNA wording re-check** — the 1990 Order's schedule defines the estuary
   area; if its text supports "both banks", the panel may say so; if not, wording
   stays as-is. Claim only what the Order's text supports.

## Acceptance criteria
- Every changed regulatory sentence maps line-by-line to a raw build-time fetch
  (the size-limits discipline; build report carries the claim→source table).
- The ten size values and species names in the table are byte-identical pre/post.
- Stamps scoped and dated per Decision 2; no stamp on anything not re-fetched.
- JS-off / offline / night / print re-verified. Print: the session sheet's rules
  block carries the new text; `fitSheet` cut order unchanged.
- No venue entry changes; no region-list/title change; hash and share untouched.
- Size budget ≤ ~2.5 KB.

## Out of scope
Welsh marks / `region:'W'` / map / chips / first-visit (the follow-on item) · chip
grouping · Welsh-language content · tide APIs · changing any NWIFCA-sourced value ·
new species cards.

## Decisions & errata (post-build)

- **24 claims machine-matched against 8 raw build-time fetches, 0 fails**, plus 4
  confirmed negatives. Sources: gov.uk bass guidance, gov.wales bass 2026 PDF
  (`pdftotext`), gov.uk + NRW rod-licence pages, NRW size-limit byelaws, gov.uk
  freshwater rod fishing rules, the Bass (Specified Areas) Order 1990, NWIFCA sizes.
- **Decision 4 confirmed a real defect in shipped copy.** The panel said "No EA rod
  licence for sea species from the shore; a licence IS needed to target salmon/sea
  trout anywhere." Two problems: (a) **no source states the sea exemption** — neither
  licence page mentions sea fishing at all (confirmed negative), so the sentence
  asserted a rule nothing backs; (b) the statutory list is **broader** than
  salmon/sea trout — gov.uk and NRW both say *salmon, trout, freshwater fish, smelt
  or eel*, identically, for England and Wales. Replaced with the transcribed list
  plus "sea species are not on it" — a statement about the list, which is
  verifiable by reading it, rather than a legal claim nothing supports.
- **The eel clause was split by country mid-build, after a self-review caught the
  build over-claiming.** Wales's byelaw is explicitly sea-inclusive ("any water out
  to 6 nautical miles"); England's rule comes off a page scoped *Freshwater rod
  fishing rules*. The first draft said "England requires every eel but conger
  released alive" without that scope — a freshwater source doing sea work. Now the
  panel names England's scope and says applying it on the shore is **this guide's
  conservation call, not a citation**. Same fix shape as the "checked vs reviewed"
  discipline: narrow the verb to what the source actually covers.
- **The Dee BNA got stronger, not softer.** The Order's schedule reads "all tidal
  waters enclosed by a line drawn 213° true from Hilbre Point to Mostyn Quay" —
  Wirral to Flintshire, so the closed area spans **both banks**, and the ban is on
  fishing *from a boat* (Explanatory Note). Panel now quotes the Order and names the
  counties rather than asserting a both-banks conclusion in its own voice.
- **Decision 3 fired as written: Wales genuinely sets almost nothing.** NRW's
  size-limit byelaws cover salmon, sea trout, brown trout and coarse fish — **no sea
  species at all** (confirmed negative: no bass, flounder, plaice, whiting or dab).
  The only Welsh sea-fish size is bass 42 cm. KNOW's Wales block says exactly that
  and offers the English table as a flagged voluntary yardstick.
- **Bass is now genuinely dual-sourced**, which is why the "everywhere" heading
  survived: gov.wales's 2026 PDF carries the same three numbers as gov.uk, in the
  same words, and both state the rules apply from the shore as well as from a boat.
- **Deviation — size budget.** +3.14 KB against a "≤ ~2.5 KB" budget. ~0.6 KB of the
  overshoot is URL text alone: NRW's size-byelaw URL is 150 characters and appears
  three times. Trimming sourced regulatory content to hit a soft byte target was the
  wrong trade; declaring the overshoot instead.
- **Deviation — one small addition beyond the spec:** `id="sizes"` on the "Can I keep
  it?" heading, so the regs panel can link to the Wales block rather than describe
  where it is. No existing `#sizes` reference or CSS rule to collide with (checked).
- **Session-sheet extractor updated** per spec item 4: `querySelector('.regs p')` →
  `querySelectorAll`, looped. Without it the entire who-sets-them paragraph would
  have silently vanished from paper — the sheet would have printed the rules while
  omitting which authority sets them.
- Testing note: clicking `#printbtn` to test the sheet calls `window.print()` and
  **hangs the browser pane on the modal**. Build the sheet by calling
  `sessionSheet()` directly instead; only that path is testable headlessly.
- Four surfaces verified: JS-off (all new copy is static markup, asserted outside
  `<script>`), offline (additions are `<a href>` only — no new fetch), night (`.regs`
  background and `.checked` colour both flip; no overflow from the long URLs), print
  (sheet carries both paragraphs, cut keys unchanged, weather still off paper).

### Review findings (Fable, 30 Jul 2026) — verdict PASS after three in-item fixes

- **The build's Decision-3 absence claim was FALSE, and the spec had named the
  source it missed.** "There is no Welsh legal size for flounder, plaice, whiting
  or dab" rested on NRW's *angling* byelaws alone. The spec's feasibility section
  said to search for the legacy NW&NW Sea Fisheries Committee byelaws; review found
  them published on gov.wales ("Inshore Fishery Legislation (North Wales)",
  text dated Sept 2011, published 2018): **saved Byelaw 19 sets ~28 sea-fish
  minimum sizes, "no person shall remove" (anglers included), and every species
  shared with the guide's table carries the identical value** — flounder 250 mm,
  plaice 270, whiting 270, dab 150, cod 350, grey mullet 200, mackerel 200, conger
  580 — because NWIFCA inherited the same committee's byelaws on the English side.
  Same numbers, different signature. The lesson for Decision-3-style fail-closed
  paths: **an absence claim needs the named candidate source actually run to
  ground, not just the first authority page checked.**
- **Two genuine nuances the corrected block now carries:** the byelaw's own bass
  line reads 360 mm — it predates the national 42 cm MCRS, which also applies, so
  42 cm governs; and the saved byelaws' area stops at the Dee's mouth (Hilbre
  Point → Hilbre Island → Point of Ayr disused lighthouse, the district's own
  river-mouth line), so on the inner Welsh Dee bank the only certain sea-fish size
  IS bass 42 cm — there, and only there, the voluntary-yardstick advice survives,
  still flagged as the guide's conservation call.
- **Two stamp misattributions fixed.** Panel: "…the licence list, both eel rules …
  re-checked against those sources" — where "those sources" were the three bass
  links, none of which sets the licence list. Footer: same shape ("against all of
  these"). Both replaced with per-source attribution ("re-checked against the
  source that sets it — …"), and the licence list's source page is now actually
  linked from the panel. A stamp that names a source list containing an unchecked
  pairing is a false stamp even when every individual fact is true.
- Review re-fetched all eight sources independently (fresh copies, own strip
  pass): 29/29 build-era claims PASS, then 27/27 on the corrected block (16
  Byelaw-19 text matches, 7 mm→cm value equalities, 4 removed-phrase negatives).
  Size table re-confirmed byte-identical throughout; final delta +4.5 KB
  (overshoot grew with the Byelaw 19 content and its 150-char gov.wales URL —
  same trade as the build's declared deviation, same reasoning).

## On pass (same commit)
CLAUDE.md: the venue-contract constraint's Wales clause updates to "Wales' regulatory
spine ships (this spec) — Welsh marks need only the venue contract"; add the forward
constraint that regulatory claims are authority-scoped and an "everywhere" claim
needs every covered authority's source. Errata here, under the convention.
