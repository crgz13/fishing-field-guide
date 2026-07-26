# Spec — ①(a) First-visit profile (location + rod-or-none)

## Goal
A stranger opening the page gets neutral, sensible defaults and two optional questions
that tailor the page. Craig's rig (RR World Traveller short tip + braid 15lb) becomes a
profile saved on his device, not the page's hardcoded identity.

## Changes (all in `fishing-field-guide.html`)

**1. Neutral defaults** — `state` default becomes
`{rod:'lightgen', line:'braid15', venue:'all', cmin:10, cmax:40}`.
Braid 15lb stays: it's the guide's brand-neutral teaching line. RR presets stay in the
`RODS` list — they're legitimate presets, just no longer the default.

**2. Static HTML matches the new default** (the no-JS view). Specstrip (lines ~277–284)
shows generic light spinning 10–30g / braid 15lb / max lead 14g (0.5oz) / hooklength 11lb /
mark "All venues". Standfirst parenthetical "(default: the Rigged & Ready World Traveller +
RR3000)" → "(default: a generic light spinning rod — set yours below)". No other copy.

**3. First-visit card**, injected by JS at the top of PLAN, shown only when there is no
URL-hash state AND no saved profile. Non-blocking, dismissible ("skip" ×). Two button rows:
- *Where will you fish?* — *Cumbria (Duddon/Barrow)* → `select('hodbarrow')` ·
  *Merseyside (Mersey/Dee)* → `select('newbrighton')` · *Not sure* → stay on `all`.
  (Each region's best-verified mark; existing `select()` does panel + map pan.)
- *What rod do you have?* — options from `RODS` presets + *"No rod yet"*. "No rod yet"
  selects `lightgen` and shows one line in the gear deck: planning around a generic light
  spinning rod (10–30g), the sensible first buy. Line hides if a real rod is chosen later.

**4. Saved profile** — localStorage key `efg-profile`: `{rod,line,venue,cmin,cmax,norod,answered}`.
- Load precedence at init: **URL hash > saved profile > neutral defaults**.
- Saved from the card, and on any user-initiated gear/venue change (one hook in `select()`,
  suppressed during initial render so opening someone's shared hash link never overwrites
  your own profile).
- All access in try/catch (file:// and private mode degrade to today's behaviour).
- Tiny "Saved on this device · reset" line in the gear deck; reset clears the key, restores
  neutral defaults, card may show again.
- CLAUDE.md rejected-list check: not "localStorage-only without export" — the URL hash
  already encodes the full profile and stays the shareable/bookmarkable export.

## Acceptance criteria
- Fresh visit (no hash/profile): lightgen + braid15 + all venues; card visible.
- Card answers apply immediately and persist across reload on a clean URL; card never
  reappears once answered or dismissed (except after reset).
- Hash links override displayed state but never overwrite a saved profile.
- Gear/venue changes via existing controls update the profile.
- JS disabled: no card, static header shows neutral defaults, everything readable.
- Offline: identical. Night mode: card legible via existing CSS vars. Print: card hidden.

## Regression risks
- Hash read/write untouched except init ordering — existing shared v3 links must resolve.
- Header edit must not break specstrip layout, print, or night styling (keep IDs/classes).
- Card injection must run after `VENUES`/controls init, before first `select()`.

## Out of scope
De-Craig copy sweep incl. `<title>` and kit-table "owned" flags (①b) · "checked <date>"
stamps, verify links, global date banner (①c) · "estimate"/"approximate" tags (①d) ·
start-here router (②) · gear inventory (④) · any profile fields beyond the five above ·
export/import UI.
