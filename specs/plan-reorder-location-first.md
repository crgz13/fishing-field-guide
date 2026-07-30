# Spec — PLAN reorder: location first, gear in one place

## Why
The first-visit card asks "Where will you fish?" and only then "What rod do you
have?" — the page's own onboarding order. The body contradicts it: PLAN opens with
two gear blocks and buries the mark decider third, even though the mark decides the
things that can hurt you (tide window, cut-off, season) and the gap list already had
to be placed after the venue because it needs both. Gear is scattered across four
areas (deck, locker, gap list, kit list) by accretion — each item landed next to
whatever existed when it shipped.

## Change — reorder only; no copy, no logic, no styling changes

### 1. New order inside the "Plan the session" section
1. `#firstvisit` (runtime card — anchored to the TOP of the section, see §3)
2. **The mark**: `.chips` → map (`#livemap` + `#mapfallback`) → `#vpanel`
3. **The tide-safety box** (`.safety`, moved up from KNOW — see decision 2)
4. **Weather check** `details` ("should I go" reads the mark, so it follows it)
5. **Gear, one cluster**: `#geardeck` → locker `details` → `#buygap`
   (today's setup → what you own → the gap between them at this mark)
6. `.regs`

Sections after PLAN keep their order: What's biting (calendar + catch log) → Kit
list → Checklist → DO → KNOW (minus the moved safety box) → LOOK UP.

### 2. The specstrip leads with the mark
`renderGear()` writes the header plate as Rod · Cast weight · Main line · Max lead ·
Hooklength · Mark. Reorder to Mark first — the plate should read in the same order
the page now decides. (One line of JS, same function, same values.)

### 3. First-visit anchor
`buildFirstVisit()` does `deck.parentNode.insertBefore(card, deck)` — anchored to
`#geardeck`, so today it sits at the top only because the deck does. Re-anchor to
the top of the PLAN section (before the chips block) or the card sinks mid-section
when the deck moves. **This is the only JS change besides §2.**

## Decisions (made now, at spec time)

1. **The rig-chain selects stay in DO.** They are the rig lesson's checker — you
   build the running ledger link by link and the page judges each link. That is
   pedagogy, not gear selection; folding it into the gear cluster would break the
   one-rig teaching path (principle 5).
2. **The tide-safety box moves INTO PLAN, directly after the mark decider.** It is
   titled "Tide safety" — it is about the going-out decision, not fish handling,
   which is what actually lives in KNOW. Earlier is more prominent, and principle 3
   only permits moves in that direction. The box itself is untouched: same element,
   same `role="note"`, same bullets, byte-identical text. KNOW keeps the fish-ID
   danger cards and the hook-in-skin card (those ARE handling).
3. **The kit list does not join the gear cluster.** It is a reading list ("in build
   order") — printed matter, not a control surface. The cluster is the three
   interactive gear surfaces only.
4. **Nothing else moves.** DO/KNOW/LOOK UP order untouched; calendar stays its own
   section directly after PLAN ("this table follows the mark selected above" stays
   true — the mark is still above it).

## Acceptance criteria
- Pure move: every relocated block is byte-identical (same ids, classes,
  attributes, inner HTML). The only JS diffs are the first-visit anchor and the
  specstrip cell order — everything else in the script block byte-identical.
- All existing behaviour re-verified: select() venue flow, hash/profile precedence,
  locker/gap/rig interplay, share button, session sheet (reads sources, not
  positions — must be provably unaffected), first-visit card appears at the top of
  PLAN on a clean load and both its questions still work.
- The safety box: still `role="note"`, text byte-identical, now BEFORE the fold of
  the venue choice; night/print styling unchanged.
- Four surfaces: JS-off (static order correct), offline, night, print (both modes).
- Anchors/router: `#plan` still lands on the section; any in-page links to moved
  blocks still resolve (ids travel with their elements).
- Tab/reading order follows the new visual order (DOM order is the tab order).

## Amendment (user, at build time)
The section subhead read "Pick your gear, pick your mark" — the old order, and stale the
moment the blocks moved. The build flagged it rather than exceed scope; the user's
answer was **"fix the subhead in this commit"**. It becomes "Pick your mark, pick your
gear." — a word swap, no new claim, and it overrides the blanket "rewording" exclusion
below for this one sentence only.

## Out of scope
Rewording anything else · restyling anything · moving the rig chain, kit list, calendar,
or any KNOW/LOOK UP content beyond the safety box · touching the hero rig diagram in
DO · venue expansion.

## On pass (same commit)
CLAUDE.md forward constraint: PLAN is location-first (mark → safety → weather →
gear); the first-visit card anchors to the section, not the deck; the rig-chain
selects belong to DO's teaching path, never to the gear cluster.

## Decisions & errata (post-build)

- **The move was executed by script with conservation assertions**, not by hand: each
  relocated block byte-identical and present exactly once, all non-whitespace
  characters conserved as a multiset. The build's first guard was wrong in an
  instructive way — it tested string equality, which a reorder fails by definition;
  the multiset is the right invariant for a pure move.
- **Positional language swept at review.** Every "above/below" that touches a moved
  block still holds in the new order: the locker's "the selects above" (the deck
  still precedes it inside the gear cluster), the offline map's "use the chips
  above", the calendar legend's "your log below", the seasons subhead's "the mark
  selected above". None needed rewording.
- **Review fixed a stale banner comment**: `<!-- SAFETY -->` in KNOW still labelled a
  wrapper that now holds only the dogbox.
- The subhead amendment ("Pick your mark, pick your gear.") was requested by the user
  at build time and recorded above — the one sanctioned rewording.
- JS diff is exactly two changes: the first-visit anchor (`#geardeck` → `#chips`) and
  the specstrip cell order (Mark first). +250 bytes total, all comments.
