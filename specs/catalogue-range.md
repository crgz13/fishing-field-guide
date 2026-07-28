# Spec — ④(a)+ catalogue range: biggest-to-smallest setups

## Goal
Extend the 51-item generic catalogue so a stranger can assemble a working setup at any
scale UK shore fishing actually uses — from ultralight/LRF (mini species off walls) up to
an 8oz heavy-beach tier. Rationale for the top end: the user's stated direction is to
cover marks around the UK ("a workhorse for fish trip research"); the heavy tier exists
for those future marks, not the current eight. Full chain per band (rod, reel, line,
leader, hooks, rig/lures, leads) so `gapNeeds()` can always name a real item.

## New items (all generic trade nominals — no brands, no prices, same discipline as ④a)
**Ultralight / LRF:** rod 1–10g · reel 2000 · braid 8lb 0.08mm · fluoro hooklengths 4lb +
6lb · Aberdeen hooks 8 + 6 · lures: 2g jig head, 3g soft, 5g metal · LEADS gains
[7,'0.25oz'] (auto-generates the catalogue bomb, and the 5–20g rod's derived max lead
becomes an honest 7g instead of "—").
**Middle hole (60–100g):** rough-ground/light-beach rod 56–113g (2–4oz, classed beach) ·
mono shock leader 40lb 0.55mm (leaderLb(113)=40 — the chain was missing it).
**Bass-band lure top:** metals 28g + 40g (the 20–60g rod can throw them; today's lures
stop at 21g).
**Heavy tier (8oz):** rod 150–227g · reel 10000 · mono main 18lb 0.40mm · mono shock
80lb 0.80mm (leaderLb(227)=80) · Aberdeen 3/0 · pulley rig (hook 3/0, rated 227g) ·
LEADS gains [227,'8oz'] + wired 227g.

## Rule-system edges this touches (single-source, shared with the gap list)
- `REELBAND.light` [2500,4000] → **[2000,4000]**; `REELBAND.beach` [6000,8000] →
  **[6000,10000]**. Nothing else in the class system changes — no fifth rod class, no
  new venue verdicts (decided: catalogue-only).
- **Known ceiling, accepted:** a true 1000-size LRF reel still scores amber/red against
  the shared light band. The honest fix is a fifth class with 8 new venue claims; not
  worth it yet. `ponytail:` comment at REELBAND names this.
- **Pedagogy guard:** the pulley rig lives in the catalogue like the flappers do, but the
  gap list must go on recommending the running ledger only — one-rig teaching path is
  untouched.

## UI
Catalogue select gains native `<optgroup>` per band (Ultralight/LRF · Light & lure ·
Bass · Beach · Heavy beach), items sorted smallest→biggest; generated leads stay one
weight-sorted group (their labels are the sizes). Additive `band` tag on items; locker
storage schema unchanged — old JSON exports must still import.

## Acceptance criteria
- rod↔reel: LRF+2000 green, heavy+10000 green; heavy+4000 still red, light+10000 still
  red (widening must not flatten the rule).
- Shock leader at 227g demands 80lb **mono**: red bare, green with the new leader, and an
  80lb braid still fails — the mono-only test is untouched.
- Custom 1–10g rod renders max lead "—" (existing null path); no negative number anywhere;
  5–20g rod now shows 7g (0.25oz).
- Gap list never names the pulley rig. Export→import round-trips new items; a pre-change
  export imports clean.
- Four surfaces pass: JS-off (no static regressions), offline, night, print.

## Out of scope
New venues or venue verdicts · fifth rod class · setup bundles ("add whole kit") ·
prices/brands (rejected list) · RODS picker presets (the 7 presets are a separate
teaching surface; catalogue feeds the locker only).

## On pass (same commit)
CLAUDE.md: record the range extension, both REELBAND widenings + the 1000-reel known
ceiling, the pulley pedagogy guard, LEADS 7g/227g, and the UK-workhorse direction note.
