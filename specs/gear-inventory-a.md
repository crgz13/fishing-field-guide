# Spec — ④(a) gear locker: data model + seed catalogue + inventory UI

## Decomposition of ④ (each sub-item its own spec/build/review cycle)
- **(a) this spec** — item schemas, seed catalogue, "my gear locker" UI, persistence +
  JSON export/import. **No rules, no verdicts** — the locker only stores and lists.
- **(b)** rules engine (~10 pairwise g/y/r rules) + verdicts rendered on the rig-chain
  SVG (ids per chain link, colour + tap-for-reason). ⚠ needs new per-venue `run`/`ground`
  data — new claimed facts, to be sourced or estimate-tagged; that fight happens in (b).
- **(c)** computed gap list replacing the hardcoded buygap box (line 1006–1017).

## Veto point 1 — single file stays (CLAUDE.md says ask first)
Estimated addition: catalogue ~4KB + UI ~6KB ≈ +10–12KB on today's 91KB. Well inside
single-file comfort; offline-first intact. **Recommend: no split.** Veto to split.

## Survey findings the design rests on
- `RODS`/`LINES` (lines 821–835) already match the ④ schemas — the catalogue reuses the
  same shapes, so a locker rod IS a valid `rodOf()` result later. `LEADS` (836) seeds the
  lead items. Bait is **session-level by prior decision — excluded from the locker.**
- Persistence idiom exists: `canStore()`/`loadProfile()` (843–848). Locker follows it
  under its own key `efg-locker` — profile and locker never share a key.
- ④(a) touches **none** of `derive()`, state, URL hash, or the first-visit card. The
  selects stay "what am I fishing today"; the locker is "what I own". Zero behaviour
  change to any existing feature is a hard acceptance criterion.

## Changes
1. **Schemas (JS object, one per type)** — exactly CLAUDE.md's: rod{min_g,max_g},
   reel{size_class}, line{type,lb,mm}, hooklength{lb}, lead{g,wired}, hook{pattern,size},
   rig{hook_size,lead_rating}, lure{g,style}. Every item: `{id, type, label, spec…}`,
   generic labels only ("3000-size fixed spool"), never brands.
2. **Seed catalogue `CATALOGUE`** — ~40–50 items across the 8 types (rods by class,
   reels 2500–8000, braid/mono at common lb, fluoro hooklengths, the existing lead
   table, Aberdeen/circle hooks 6–1/0, running-ledger + flapper rigs, metals/softs).
3. **Locker UI** — one closed `<details class="ref">` **"My gear locker"** in PLAN after
   the gear deck. Inside: current items as a list grouped by type (label + spec + remove
   ×), an "add" flow = type `<select>` → catalogue `<select>` → Add button, plus
   add-custom (same fields as the schema, number/text inputs). Words-first: closed by
   default, page complete without it; empty locker shows one sentence saying what it's
   for and that ④(b)'s checks will use it.
4. **Persistence** — localStorage `efg-locker` (same try/catch idiom). **Export** =
   `<a download="gear-locker.json">` built from a Blob; **import** = `<input type=file>`
   + JSON.parse with a one-line schema check (reject non-arrays / unknown types with a
   visible message, never silently). Native elements only, no new deps.

## Acceptance criteria
- JS-off: page reads exactly as today (locker details shows static explainer copy only).
- Zero regression: profile save/load, URL hash, first-visit card, derive outputs
  byte-identical for a fixed gear selection before/after.
- Export → clear locker → import round-trips to an identical locker.
- Corrupt/foreign JSON import: visible rejection message, locker untouched.
- Night mode + print: locker list legible; details opens on print via the existing
  generic listener (verify, don't assume).
- file:// with storage blocked: locker works for the session, degrades without errors.
- No brand name anywhere in catalogue or copy.

## Regression risks
- New `<details>` inside PLAN is caught by the print-open listener automatically — that
  is *wanted*; confirm an empty locker prints as one clean sentence, not a broken form.
- The chip-sweep test gotcha: clicking around PLAN must not write `efg-locker`
  spuriously — only explicit add/remove/import writes.

## Out of scope
All rules/verdicts/gap logic ((b),(c)) · venue `run`/`ground` data · bait items ·
any change to derive(), presets, or the specstrip · brand/price/availability data
(rejected list).

## On pass (same commit)
CLAUDE.md: mark ④(a) done in the roadmap; record the single-file decision and the
locker's location (closed details in PLAN).
