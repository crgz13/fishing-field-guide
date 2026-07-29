# Spec — ④(b) rules engine + verdicts on the rig-chain diagram

## Goal
Pairwise g/y/r compatibility rules, each with its "why", rendered as link colours on the
running-ledger hero SVG (line 444) with tap-for-reason. Engine is pure functions over
item objects — ④(c)'s gap list reuses it unchanged. **No venue datum changes.**

## Decisions already made (do not reopen)
- **Run rule = decision A**: fires only where a sourced figure exists — today that is
  New Brighton alone ("5–6oz to hold mid-tide"). Everywhere else the rule returns null
  and the link is simply not judged on run. No estimated holding weights, ever.
- **Ground classes derived from the existing sourced `Ground` stats strings** (all 8
  classify: snaggy/mussel→snaggy; rock/blocks/slag/groynes→mixed; clean or sand-only→
  clean; mud→soft), stored as a per-venue `ground` field **plus a code comment naming
  the derivation** so it's visibly a re-labelling of sourced prose, not a new claim.
  Each inherits its venue's existing confidence badge; no new badges.

## The session rig (new state, not persisted)
`RIG = {reel:null, lead:<derived max lead>, hooklength:<derived lb>, hook:<Aberdeen 4>,
bait:null, leader:null}` — rod/line/venue come from existing state. Defaults follow
`derive()`, so the untouched page judges the rig the page itself recommends (must be
all-green for every rod preset at its derived lead — acceptance test). One row of
compact selects under the hero diagram fills the slots; options come from the locker
(optgroup "In your locker") then the catalogue. reel/bait/leader default "not set" —
rules touching an unset slot return null, never guess.

## Rules (pure: rule(a,b,ctx) → {v:'g'|'y'|'r', why} | null)
1. **rod↔lead**: ≤max−12g → g; ≤max → y (no margin for bait+rig); >max → r. Why quotes
   the −12g allowance (principle 4).
2. **line↔lead shock leader**: lead >57g needs line/leader ≥ 10lb per oz of lead —
   met → g; unmet → **hard red, safety wording, never softened**. Leader slot satisfies it.
3. **line↔hooklength weak-link**: hooklength ≤ ~80% of main → g; 80–100% → y; ≥ main →
   r ("a snag now breaks mid-line, not at the hook").
4. **rod↔reel**: light/lure→2500–4000, bass→4000–6000, beach→6000+ → g; one class out →
   y; further → r. Convention, and the why says so.
5. **reel↔line**: braid → g on any size; mono ≥15lb on ≤3000 → y (capacity + coil
   memory); mono on 4000+ → g.
6. **hook↔bait**: from the bait table's own "Best for"/notes vocabulary (worm 4–1/0;
   strips 2–2/0; prawn 2–1/0 etc.) → g/y with why; never r (bait choice is never
   dangerous).
7. **lead↔venue run**: decision A above. New Brighton: <85g → y "5–6oz to hold
   mid-tide — fish the LW window your verdict already points at, or step up".
8. **rig/lead↔ground**: snaggy ground + wired lead → y (grip digs into snags; plain
   bomb pulls free); flapper rigs on snaggy → y (more hardware to lose); clean → g.
   Why names the venue's actual Ground string.
9. **bait↔venue+month**: bait's target species (the bait table's existing "Best for"
   column, duplicated as one JS map — same wording, no new claims) vs the species-month
   chars: any p → g; m → y; all n → r. **Why must carry the estimate provenance** ("month
   bands are calibrated estimates — see the calendar tag").
Lure↔venue+month belongs to ④(c) where lure verdicts have a render surface (gap list).

## SVG rendering
- Wrap chain strokes in `<g id="seg-reel|line|lead|hooklength|hook|bait">` — reuse
  existing shapes, move nothing. JS sets stroke per worst verdict of the rules touching
  that slot. Verdict colours reuse the g/y/r vocabulary the venue-panel fit verdicts
  already established — deliberately NOT the teal accent, which means "diagram", not
  "judgement". Exact values read from the existing fit CSS at build, not invented.
- Verdict colours are set inline, so night-mode attribute selectors stop matching —
  links keep day verdict colours at night, the exact precedent badges set. Untouched
  SVG elements keep their attributes so existing night rules still match (regression
  criterion).
- Tap/click a segment (and keyboard: tabindex+enter) → reason box under the diagram
  (`aria-live="polite"`): item name + every rule verdict + why. The box names the
  *selected* item so the static teaching labels ("braid 15 lb", "ABERDEEN size 4")
  can't be misread as the judged rig; those labels stay untouched teaching copy.

## Acceptance criteria
- Every rod preset at its own derived defaults → all judged links green.
- Beachcaster + mono 15lb + 170g, no leader → hard red lead link, mandatory-leader
  copy; adding the 30lb leader flips it green.
- Run rule: verdict mentioning holding weight appears at New Brighton only (all 9
  venues swept).
- Unset slots produce no verdict and no fake green — links with no judged rule keep
  their original stroke.
- JS-off: diagram byte-identical to today. Night: verdict colours persist (badge
  precedent), all untouched elements still flip. Print: colours print; reason box
  prints only if opened — acceptable, noted.
- No change to derive(), VENUES data values, species strings, or any badge.

## Regression risks
- Grouping strokes must not alter render order (strokes overlap at joints) — visual
  diff day+night before/after with no verdicts applied.
- `body.night svg [stroke=…]` selectors: grouping keeps attributes on the child
  shapes, not the `<g>`, or night mode breaks for untouched states.
- Chip sweep must not write anything: RIG is session state, zero persistence.

## Out of scope
Gap list (④c) · lure rules render · making teaching labels dynamic · persisting RIG ·
run/ground data for other venues · any new venue fact.

## On pass (same commit)
CLAUDE.md: tick ④(b); record decision A's wording and the ground-derivation map as
the venue `ground` field's provenance.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ④(b) on 30 Jul 2026.

④(b) was initially blocked: the rules needed per-venue run/ground data that did not
exist. Resolved without inventing any:

- **GROUND is a re-labelling** of each venue's existing sourced "Ground" stat
  (snaggy / mixed / clean / soft). No new claims; the confidence badge is inherited.
- **RUN carries a figure ONLY where a source gives one** — today New Brighton alone
  ("5–6oz to hold mid-tide"). Everywhere else the rule stays **SILENT** rather than
  estimating a holding weight. This is **decision A**: a guessed holding weight is a
  safety-adjacent invention about a real tidal mark. Silent beats invented.
  (Decision A was invoked again in ⑤ to drop the onshore/offshore gloss.)

9 rules render as link colours on the rig-chain SVG, tap/keyboard for reasons; unjudged
links keep their original stroke. ~10 pairwise/chain rules, each returning g/y/r +
reason: rod↔lead(+12g), rod↔reel, reel↔line capacity, line↔hooklength weak-link,
line↔lead shock-leader rule (hard red >57g without one), hook↔bait, lead↔venue run,
bait/lure↔venue+month via the species calendar, rig↔ground snags.

### Errata, load-bearing
- **A beachcaster at its own derived 142g max lead on the default 15lb line is
  correctly RED** (needs ~50lb shock leader). That is the safety rule working — do not
  "fix" it to make defaults all-green.
- **Shock-leader maths rounds to nominal ounces** (142g = 5oz = 50lb). `ceil()` wrongly
  demanded 60lb.

Both are retained in CLAUDE.md as invariants.
