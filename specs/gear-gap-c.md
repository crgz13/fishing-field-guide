# Spec — ④(c) computed gap list replaces the hardcoded buygap box

## Goal
`renderBuyGap()`'s three hardcoded `buy` lists die. The box keeps its div, CSS, heading
and trigger (venue ≠ all, current rod fit y/r, another class fits g) but computes its
list from the locker + catalogue + the ④(b) engine's own constants. Plus the
lure↔venue+month rule deferred from ④(b), which renders here.

## Computed needs (per target class, per venue)
Each need: first LOCKER item passing the check → "✓ in your locker: label (spec)";
else first CATALOGUE item passing → "add: generic label (spec) — why". All owned →
headline flips to "your locker already covers it". Empty locker degrades to a fully
generic list ≈ today's box minus prices.

- **Rod** — locker rod with `clsOf(max_g)` = target, where `clsOf` reuses rodOf's
  custom thresholds exactly (<45 light, <80 bass, else beach — no new bands). Target
  'lure' accepts a light-class rod; its line says what the old box said: a light
  spinner covers lure work, a dedicated 7–28g rod just has a softer tip. Fallback
  c-rod3/5/6.
- **Reel** — size inside the class band. Extract `REELBAND={light/lure:[2500,4000],
  bass:[4000,6000],beach:[6000,8000]}` and make rule `rod-reel` read it too — one
  source, no drift. Behaviour-identical (probe before/after).
- **Shock leader** — only if the target rod's derived max lead >57g: leader lb ≥
  `leaderLb(g)` = `Math.round(g/28.35)*10`, extracted so rule `shock-leader` and the
  gap list share the formula. Mandatory wording stays the rule's.
- **Leads** — locker lead ≤ target rod's derived max AND ≥ `RUN[k].min` where RUN is
  sourced (**decision A: the floor exists at New Brighton alone; silent elsewhere**).
  Where the floor bites, the line quotes `RUN[k].why` verbatim.
- **Rig** (beach target only) — locker rig with `lead_rating` ≥ target lead. Generic
  fallback is **c-rig2 running ledger heavy, NOT a flapper** — the old box's flapper
  suggestion contradicted the one-rig pedagogy; computing fixes it.

Dropped from the old box, deliberately: **£ price hints** (rejected list bans
price/availability data; the spec catalogue carries none) and the **rod rest** line
(not representable spec-based; venue blurbs keep practical notes). "Or fish it on your
rod's terms: <fit reason>" closing line stays.

## Lure↔venue+month rule
Fires only when the target class is 'lure' (its render surface). Reads the venue's
existing calendar rows whose species name contains 'bass' — the guide's own lure
quarry ("lure bass May–Sept"), zero new species claims — current-month char → g/y/r
dot line: "The calendar puts <who> at prime/possible/unlikely here in <month>." +
the same estimate-provenance sentence rule `bait-month` uses.

## Wiring
Re-render on venue select (existing), rod/line change, and locker add/remove/import —
verify at build which paths already reach it. RIG stays session-only; the gap list
reads the locker, never writes it.

## Acceptance criteria
- Defaults (travel rod, braid 15, all venues) → no box. Unchanged behaviour.
- New Brighton + light rod, empty locker → generic list: beach rod, 6000–8000 reel,
  lead + leader sized by `maxLead`/`leaderLb` off the target rod, RUN.why quoted,
  running ledger heavy. No £, no brands, no flapper.
  (Corrected at build: I first wrote "142–170g leads, 60lb leader" here — wrong. A
  100–170g rod's derived ceiling is 170−12=158 → **142g (5oz)**, so the leader is
  **50lb**. 170g/60lb is unreachable from any rod in the catalogue; it exists for the
  rig-chain, where the lead is picked directly. The mechanism was right, my arithmetic
  wasn't.)
- Add matching items to locker → lines flip to owned; all owned → headline flips.
- Full venue × rod sweep: RUN floor text at New Brighton only; lure-month line only
  where target class is lure, quoting only bass rows, always with the estimate sentence.
  Concrete reachable case (checked at spec time): beachcaster preset at Thurstaston —
  beach fit y, lure fit g → target 'lure' → the line must render there.
- `rod-reel` and `shock-leader` rule outputs byte-identical before/after the
  constant extraction (probe set incl. 142g→50lb, 170g→60lb).
- Old `buy` object deleted; no `£` in the gap code. No change to VENUES data, fit
  strings, badges, derive()/gearCalc numbers.
- JS-off: empty div (as today). Night: box + inline VCOL dots as ④(b) precedent.
  Print: prints as rendered. Chip sweep writes nothing new.

## Out of scope
Persisting RIG · lure slot on the rig chain · price/availability anything · new venue
facts · rod-rest/accessory schema · changing the box's trigger.

## On pass (same commit)
CLAUDE.md: tick ④(c); record the price-hint drop, the running-ledger fallback (pedagogy
fix), lure targets = calendar 'bass' rows, and REELBAND/leaderLb as shared constants.
