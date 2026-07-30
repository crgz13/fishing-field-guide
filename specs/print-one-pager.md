# Spec — print one-pager ("tonight's session")

## Why (roadmap ⑤, final item)
The guide prints today as the whole document — right for "hand someone the guide",
wrong for "I'm fishing in an hour". The one-pager is a single A4 sheet carrying only
what you take to the water: your numbers, your mark, the safety lines. Paper works
with wet hands and a dead phone battery — the offline-first principle's last mile.

## Change

### 1. A second print mode, button-triggered — the default print is untouched
`Cmd+P` today prints the full guide and still will. A new button in the gear deck's
action row, next to "Share this setup":

```html
<button type="button" class="act" id="printbtn" hidden>Print tonight's session</button>
```

(`hidden` until JS init — the `#sharewrap`/`savedline` pattern; JS-off shows no dead
control and default print is unaffected.)

Click → build the sheet → `document.body.classList.add('session')` →
`window.print()` → on `afterprint`, remove the class. Under `@media print` with
`body.session`, everything except `#sessionsheet` is hidden; on screen the sheet is
never visible. **Printing is a READ**: no state, storage, or hash mutation.

### 2. The sheet — only content the page already carries, re-set for paper
`#sessionsheet` is an empty static `<div hidden>`; JS fills it at click time from the
same sources the screen uses (one source, can't drift):

- **Title line:** "Tonight's session — <mark name> — <session date>". The date is the
  sun-picker's date (`SUNDATE` or today), labelled as the session date.
- **Setup:** rod + line labels; the derived strip verbatim (max lead, lure window,
  hooklength, drag, shock-leader verdict). **The two why-formulas ride along in small
  print** (max lead = rod max − 12g; hooklength = main − ~25%) — principle 4 applies
  on paper more, not less.
- **Mark:** stats grid (ground, window, tide ceiling, season, parking, tide-table
  scale), the hazard line, sub-spots **with their badges as text** (`[verified]` /
  `[anecdotal]`) — text keeps the attestation axis alive in monochrome; and the
  tackle-shops line (phone numbers matter at the water).
- **Sun times** for the session date, with the existing flat-horizon caveat.
- **Tides:** the caveat, prominent: this page carries no live tide data — check a
  tide table you trust; plus the venue's tide window/ceiling from its stats.
- **Safety lines:** the safety-box bullets verbatim (cut-off, weever hot-water
  protocol, hook-in-skin drill, shock-leader rule) plus 999/112. Never summarised,
  never softened.
- **Regs line:** the bass size/season line and eels-always-released, copied from the
  page's own (already verified) text, with its existing verification date. The sheet
  adds "printed <date> — newest rules at <live URL>": **"printed" is a print stamp,
  never a verification stamp.**

### 3. Print-colour robustness for BOTH print modes (assigned from redesign errata)
Browsers don't print backgrounds by default, so light-text-on-ink (`th`, `.watch`,
`.safety`, the card tabs) prints white-on-white today. Fix in `@media print`:
flip these to dark text on white with an ink border. The sheet itself is designed
pure ink-on-white — zero reliance on backgrounds.

### 4. Night mode never reaches paper
`body.night` prints red-on-black today (toner-eating, unreadable). One print rule
resets the night variables to the day palette. Applies to both print modes.

## Decisions (made now, at spec time)

1. **The default print stays the full guide.** A one-pager that replaces it would
   orphan "print the guide and hand it over" — the offline path the footer promises.
   Two modes: default = document, button = session sheet.
2. **Weather is EXCLUDED from the sheet.** A verdict printed Monday is stale by
   Wednesday and reads as current — the false-stamp rule for paper. Tides guidance
   (the safety axis) is on the sheet precisely because it is date-independent
   (windows and ceilings, never times).
3. **No knot diagram, no rig diagram.** The sheet is a reference card, not a
   tutorial — the teaching lives in the guide. Rejected for space; revisit only if
   the sheet fits one page with room to spare.
4. **Badges become text, never disappear.** `[verified]`/`[anecdotal]` in square
   brackets — evidence honesty survives monochrome and background-less printing.
5. **The sheet is built at click time, printed, and the class removed on
   `afterprint`** — no stale sheet DOM persisting across venue changes; the existing
   `beforeprint` details-opener stays untouched (harmless: the guide is hidden in
   session mode anyway).
6. **One page is a hard budget.** Content is cut (in this order: shops → sub-spots →
   regs line) before type shrinks below legibility (~9pt floor). The safety lines,
   derived numbers + whys, tides caveat, and hazard are never the cut.

## Acceptance criteria
- Default `Cmd+P` output unchanged except: th/.watch/.safety/tabs readable with
  backgrounds off, and night mode printing day palette.
- Button click → print shows only the sheet; `afterprint` restores everything;
  screen never shows the sheet; a second venue change + print rebuilds it fresh.
- The sheet fits ONE A4 portrait page: under print emulation (with a dispatched
  `beforeprint`, per the known gotcha), the sheet's rendered height at A4 width
  stays within one page (≈1050px at 96dpi with default margins).
- Every number on the sheet is generated by the same functions that render the
  screen (gearCalc/VENUES/sunHTML) — zero hand-copied values; the two why-lines
  present; safety bullets byte-identical to the safety box's text content.
- No mutation: state, localStorage, hash identical before/after a print cycle.
- JS-off: no button, default print works. Night on screen + print → day on paper.
- Four surfaces re-checked; `writeHash`/`saveProfile` untouched.

## Out of scope
PDF generation · print preview UI on screen · multi-venue sheets ("all marks"
prints the venue picker's caveat instead: pick one mark first — a sheet for
"everywhere" would be eight sheets) · QR code of the share link (rejected in ⑤
share item) · changing what the full-guide print includes.

## On pass (same commit)
CLAUDE.md forward constraints: two print modes (default document / session sheet),
weather-never-on-paper, badges-as-text-in-print, printed-is-not-verified. Errata to
this file under the convention.

## Decisions & errata (post-build)

- **"— ask for the Coastguard" was dropped at review, fail-closed.** The build added it
  beyond the spec's sanctioned "999/112". Review tried to ground it: RNLI safety pages
  are JS-rendered (empty to curl — the St John precedent), and gov.uk's
  report-coastguard-emergency page does not carry the phrase in its static HTML. No raw
  source at hand → the clause drops; 999/112 stay (spec-sanctioned at spec time). If a
  raw primary source is ever fetched, the clause can return with its citation.
- **`@page{margin:11mm}` is global** — CSS cannot scope `@page` to `body.session`, so
  default full-guide print margins also move from the browser's ~12.7mm to 11mm.
  Accepted: cosmetic, no content change; named pages (`page:` property) were rejected
  as flaky across engines. Recorded because "default print unchanged" is now "default
  print unchanged except margins and the colour-robustness fixes".
- **Night is stripped by JS in `beforeprint`, not fought in CSS** — undoing ~20
  `body.night` rules inside `@media print` needed a sledgehammer; the class strip fixes
  BOTH print modes in three lines and restores on `afterprint`.
- **Vertical rhythm bought the space, not cuts:** at the 9pt floor with the prototype's
  spacing, shops + sub-spots were cut on every mark — against the spec's own "phone
  numbers matter at the water". Tightened heading margins and line-height instead; all
  24 venue × rod combinations now fit (worst 996px of 1039) with **nothing cut**.
- **The cut mechanism ships dormant** as the safety net for venue expansion, in the
  declared order (shops → sub-spots → regs), and it is never silent: the sheet's footer
  says what was trimmed and that the guide carries it.
- **The shock-leader line calls the live `RULES` entry** with the derived max lead —
  never a second copy of the maths. The beachcaster case prints the red
  SHOCK LEADER MANDATORY verdict.
- The safety bullet "Full card in fish ID" prints as-is — a dead reference on paper,
  kept because safety text is never re-worded. If it ever grates, the fix is changing
  the SOURCE bullet, not the sheet.
- `#printbtn` sits in its own `#printwrap` (not inside `#sharewrap`) so the share note
  stays attached to the share button.
