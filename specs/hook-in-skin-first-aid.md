# Spec — hook-in-skin first aid card

## Why (roadmap ⑤)
The guide teaches a barbed hook on a beginner's rig, unhooking with wet hands, and hooks
around dogs — but says nothing about the day the hook ends up in the angler. The weever
precedent set the pattern: the hazard gets a drawn card, a safety-list line, and honest
sourcing. This is the other half of that first-aid pair.

## Change

### 1. Third danger-card in the fish-ID card row
`figure.danger-card` after the weever card, drawn SVG in house style (fingertip with a
hook past the barb; red title **"HOOK IN SKIN — NEVER YANK"**). The figcaption is a short
decision list, one line per branch:

- Point in but the **barb still showing** → back it out the way it came, clean the wound,
  cover it.
- **Barb under the skin** → stop. Cut the line, tape the hook flat so it can't move, and
  go to A&E / an urgent treatment centre. Pulling or pushing tears more than the hook did.
- **Anywhere near an eye** → don't touch it, cover it if you can without pressure, **999**.
- Any hook wound: clean it well — sea water and bait wounds infect; mention your tetanus
  status when treated (exact wording from the NHS source at build time).
- Prevention: **crush the barb flat with pliers before you fish** — a barbless hook backs
  out of you the same way it backs out of a flounder.

**The wording above is direction, not shippable text** — see decision 3: every medical
point must be transcribed from the raw source at build time.

### 2. One bullet in the safety list
The weever pattern: full drill on the card, one-line version in the safety box.
"Hook in your skin: barb showing → back it out; barb buried → cut the line, tape the hook
still, A&E — never yank. Card in fish ID."

### 3. Pointer in DO aftercare
One clause appended to the land/unhook/release paragraph: if the hook finds you instead,
the first-aid card in KNOW is the drill. No new claims — a signpost.

### 4. Sources line
A `.checked` line under the card row: "First-aid points verified against <NHS page(s)> on
<build date>", linked. The page banner date is NOT bumped — the whole page is not being
re-reviewed.

## Decisions (made now, at spec time)

1. **No field removal of a buried barb is taught.** String-yank and push-through-and-snip
   exist; lay first-aid consensus (NHS / St John) for embedded objects is secure it and
   get treatment, and the one-path pedagogy applies to emergencies more, not less. They
   do not go in a reference section either — a medical procedure is not tackle.
2. **Prevention teaches crushing the barb.** This is a NEW teaching claim (the guide has
   never said it) — flagged for approval. Standard beginner practice, aligns with the
   release-first aftercare, makes the whole card mostly unnecessary.
3. **Fail-closed sourcing — the size-limits rule applies to medical content.** Every
   shipped medical statement transcribed at build time from raw NHS HTML (embedded /
   foreign objects, cuts and grazes, tetanus); summarisers are not a source; a point that
   cannot be grounded is dropped, not softened. The build report maps each shipped line
   to its source line.
4. **Visible, not collapsed** — same disclosure rule as the size table: emergency
   instructions behind a toggle is the wrong level.
5. **No proximity claims** ("A&E is close") — the Duddon marks are not close to Furness
   General. The copy names the destination, never the distance.

## Acceptance criteria
- Static HTML only: zero JS, storage, network. JS-off and offline identical by
  construction; night mode inherits `.danger-card` styling (verify red-on-black); print
  shows card + caption (verify).
- Every medical statement in the shipped copy maps to a cited raw-source line in the
  build report.
- "Never yank" is the loudest element after the title; barb-buried → A&E branch present;
  eye → 999 present.
- Weever card, size table, and existing safety bullets untouched except the one new
  bullet and the one aftercare clause.
- Page banner date unchanged; the new `.checked` line carries its own verified date.

## Out of scope
Dog-swallowed-hook vet advice (dog box covers it) · remote-coast self-removal techniques ·
general wound care beyond the hook wound (bleeding control, etc.).

## On pass (same commit)
CLAUDE.md ⑤ records: card done + the decisions above (no-removal stance, crush-the-barb
as a new teaching claim, fail-closed NHS transcription, visible placement, no proximity
claims).

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

Third danger-card (full-width `.fullcard`, `grid-column:1/-1` — five bullets do not fit a
230px grid cell) + one safety-box bullet + one aftercare pointer clause + its own scoped
stamp.

### The size-limits fail-closed rule extends to medical content, and it bit four times
1. **The spec's "back it out the way it came" was DROPPED.** No NHS page sanctions
   self-removal — `/conditions/splinters/` is a 404. The aftercare sentence saying exactly
   that is about a flounder, and does not transfer to human skin under a "verified" stamp.
2. **Sea-water infection dropped** — NHS grounds tetanus risk in soil/manure only.
3. **"Tape the hook flat" dropped** — the St John's page is JS-rendered, empty to `curl`,
   so unusable as a transcription source.
4. **The stamp says "Hook-in-skin first aid verified…", NOT "First-aid points verified…"**
   The wider wording, sitting below the card row, would have falsely stamped the weever
   hot-water protocol with three pages that never mention it.

### The teaching stance
The card teaches **NO removal technique for a buried barb** — cut the line, leave it, A&E.
A medical procedure is not tackle, so the string-yank lives nowhere, **not even in
reference**.

Crush-the-barb prevention ships labelled "(angling practice, not medical advice)" —
provenance split *inside* one bullet.

"Go to A&E" deliberately narrows NHS's "Call 999 or go to A&E": the NHS attribution covers
only the do-not-remove rule, matched verbatim. Do-not-drive is kept only on the eye branch,
where the NHS box plainly applies.

Skin-band fill reuses the flounder's `#e0d4b4`, so night mode recolours it with zero new
palette rules.

### Verification
Review re-fetched all three pages independently: 13 claims matched, 3 absences confirmed,
0 fails.
