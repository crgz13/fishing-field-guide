# Spec — rig-chain presentation: make the checker read rod → hook

## Problem (observed, 2026-07-28)
The ④(b) checker under the hero rig diagram doesn't read as a chain. The dropdowns render
in no physical order (Lead, Hooklength, Hook, Reel, Shock leader, Bait); once a slot is
filled its name vanishes (a default page shows three unlabelled boxes — "Plain bomb
0.5oz — 14g" with nothing saying it's the lead); rod and main line are judged but invisible
here; verdicts only paint the diagram, which scrolls away from the selects on a phone; the
shock-leader verdict lands on the *lead* segment while the leader's own dropdown shows
nothing; the main-line segment is tappable but always says "nothing to judge"; and the
hook option reads "Aberdeen — Aberdeen size 4".

## Change (decided with user, all three recommended options taken)

### 1. Vertical chain list replaces the flex row of selects
`#rigslots` becomes a top-to-bottom list in physical order:
**Rod → Reel → Main line → Shock leader → Lead → Hooklength → Hook → Bait.**
Each row: always-visible link name · the control · a verdict dot.
- Dot colour = the slot's worst verdict (VCOL); hollow/neutral when not judged.
- Tapping the row's name/dot calls the same `showWhy` the diagram taps use; `#rigwhy`
  stays where it is. Diagram tap behaviour and SEGWIRED unchanged.
- A CSS-only connector (left spine or numbering) so it visibly reads as one chain.
  No new JS framework, no library.

### 2. Read-only ends
Rod and Main line render as non-editable rows showing the current PLAN setup
("from your setup — change it in PLAN"). They already update on rod/line change because
those listeners call `buildRig()`. Main line's why-text changes from "nothing to judge
here yet" to a pointer: it is judged through the weak-link rule (shown on the hooklength)
and the shock-leader rule (shown on the lead/leader). No new claims — a signpost.

### 3. Leader row shows the shock-leader verdict
Add `'leader'` to the shock-leader rule's `slots`. `paintRig()` iterates only the six
`seg-` ids so the SVG is untouched; the list row's dot picks up `res.leader`, and
`showWhy('leader')` shows the same why. This is a mirror of an existing verdict at the
place the user set the item — not a new judgement.

### 4. Diagram gains a rod butt
Short rod-handle graphic left of the reel, static "ROD" title, sub-label = the current
rod's name (rewritten by `buildRig()`, like the seg strokes it's a "your gear" overlay on
the worked example). Decorative — not tappable, no segment, no rule. The rc-intro gains
one clause noting the rod name is yours while the other labels stay the worked example.

### 5. Option-label de-dupe
Compose `label — spec` only when the spec doesn't already start with the label
("Aberdeen size 4", not "Aberdeen — Aberdeen size 4"). Rig-slot selects only; the locker
UI's own list is out of scope (separate item if wanted).

## Acceptance criteria
- Default page: list reads Rod→Bait top-to-bottom, every row labelled, the three
  defaulted slots still preselected, dots match the diagram's segment colours.
- Beachcaster + 142g default on 15lb braid: lead row AND leader row show red
  (the deliberate default — do not "fix" it); adding the 50lb mono leader flips both
  green; an 80lb braid leader stays red (mono-only rule untouched).
- Rod/line rows change when PLAN's pickers change, with no page reload.
- Tapping any row = same why content as tapping its diagram segment. No handler
  stacking after repeated rod/line/locker changes (the SEGWIRED lesson).
- JS-off: no regression — the list host is empty as today; new static content is only
  the SVG rod butt + one rc-intro clause. Night mode styles the new rows (body.night
  overrides). Print: controls hidden as today, diagram + caption print. Offline: no
  network anywhere in this change.
- No verdict changes anywhere except `res.leader` newly existing as a mirror.

## Out of scope
New rules or venue claims · overall summary headline (dots per row cover at-a-glance;
revisit if wanted) · locker UI label composition · touching the RULES maths.

## On pass (same commit)
CLAUDE.md records: chain-order presentation, read-only ends, leader-verdict mirror,
rod butt as "your gear" overlay, label de-dupe.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

### The problem it fixed
The ④(b) checker was a flex row of six unordered dropdowns that **lost their slot name
once filled** — a default page showed three unlabelled boxes. It started mid-air at the
reel, and painted verdicts only on the diagram, which scrolls away from the controls on a
phone.

Now a vertical `.chain` list in the order the tackle runs:
**Rod → Reel → Main line → Shock leader → Lead → Hooklength → Hook → Bait**, each row
`label · control · verdict dot`, tap for the same why the diagram taps give.

### Decisions
- **CSS grid, not flex** (`120px minmax(0,1fr)`) — a long read-only value wraps inside its
  own column instead of dropping under the label and breaking alignment down the chain.
- **Rod + main line are read-only rows** fed from the PLAN setup, and they carry **NO
  verdict of their own** — what they are PAIRED with is what gets judged. Their dots stay
  hollow and `ENDWHY` points at where the verdict actually lives. This replaced main
  line's old "nothing to judge here yet", which was false over a link that plainly matters.
- **The shock-leader rule gained a second slot** (`slots:['lead','leader']`) so the verdict
  also shows where the user sets the leader. Same verdict in two places, **NOT** a new
  judgement — `paintRig()` still iterates only the six `seg-` ids, so the SVG is untouched.
- The diagram gained a **rod butt** left of the reel (viewBox `0 0 900 240` →
  `-120 0 1020 240`; the water path was extended to -120 or it would stop short). Its
  sub-label is the ONLY dynamic text in the diagram, which is why the intro says "only the
  rod name under it is yours".
- Option labels de-duped ("Aberdeen size 4", not "Aberdeen — Aberdeen size 4").
- `SLOTS` is gone, replaced by `CHAIN` (third field 1 = editable here, 0 = from PLAN).
  `SEGS` and `VWORD` are shared so the segment and row aria-labels cannot drift.

### The SEGWIRED boundary — load-bearing
**The chain rows are rebuilt whole on every change, so their listeners die with the nodes.
Do NOT put them behind `SEGWIRED`.** That guard is only for the static SVG segments.

### Verification
64 assertions, including 6 rod classes × 9 venues with row dots and segment strokes
agreeing, and the mono-only leader rule re-checked 4 ways (bare / mono50 / braid80 /
untyped).

**A green dot on an unset Shock leader under 57g is CORRECT** — the rule's own words are
"no shock leader needed", and green means ok.

### Erratum found by review (fixed in the same commit, per the split-ride-along rule)
The FIRST-VISIT card's rod buttons never ran `defaultRig();buildRig();`. Pre-existing —
the default lead already went stale against the chosen rod — but invisible until the chain
displayed the rod's name. **Any new handler that changes `state.rod` or `state.line` must
end with that pair.** The venue ("where") buttons do not need it, because `select()`
already calls `paintRig()`.
