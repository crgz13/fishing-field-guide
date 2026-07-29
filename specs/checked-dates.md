# Spec — ①(c) Checked-date stamps + verify links + global date banner (+ print-details fix)

## Goal
Every perishable fact (regs, phone numbers, tide ceilings, parking) carries a visible
"checked <date>" stamp; regs carry verify links; one global date banner. Rider (user
instruction 2026-07-26): fix the pre-existing print-closed-details bug (task chip) in the
same commit. All in `fishing-field-guide.html`.

**Date convention:** the literal string `26 Jul 2026` appears verbatim in every stamp —
greppable, updated by find-replace. No JS date logic (no-JS view must show it).

> **Build amendment (2026-07-26) — verb honesty.** The specced wording ("perishable facts
> … checked", "parking, phone numbers & tide windows checked") claims a verification that
> was not performed: no shop phone number, postcode or tide ceiling was called or sourced
> during this build. Only the bass figures were checked against a live source. Shipping
> "checked" on the rest would manufacture an evidence badge — the exact failure principle
> #2 exists to prevent. Stamps now say **"reviewed"** (a date plus a re-verify nudge, no
> claim of source-checking) and only the regs box says **"verified against gov.uk"**,
> naming the three figures that actually were. Upgrading the venue stamp to "checked"
> means phoning the four shops — a separate job, not this item.

## Changes

**1. Global banner (after `</div>` closing specstrip, line 293)** — static HTML:
`<p class="checked">Perishable facts — rules, phone numbers, parking, tide windows —
checked 26 Jul 2026. Re-verify before you travel.</p>`
One CSS rule `.checked{…}` — small, muted, colours via existing CSS vars only so night
mode inherits with zero extra rules.

**2. Regs box (line 344)** — minimal-diff edit of the existing closing sentence:
"Rules are reviewed annually — re-check **NWIFCA**/​**gov.uk**" becomes two real links
(NWIFCA → `https://www.nw-ifca.gov.uk/`, gov.uk bass rules →
`https://www.gov.uk/government/publications/bass-industry-guidance-2026/bass-fishing-guidance-2025#recreational-bass-fishing`,
both `target="_blank" rel="noopener"`), sentence ends "(checked 26 Jul 2026)".
**Phase 2 must curl both URLs for HTTP 200 before commit** — a dead verify link is worse
than none.

> Build note (2026-07-26): the URL originally specced (`/guidance/bass-fishing-guidance`)
> returned 404. Replaced via the gov.uk search API with the live MMO statutory guidance,
> deep-linked to §6 "Recreational bass fishing" — 200, no redirect, last updated
> 27 Feb 2026, and it confirms all three baked-in facts verbatim (42 cm MCRS, three fish
> per day, catch-and-release 1 Feb–31 Mar). Link text is "gov.uk bass rules" because the
> destination is titled "Bass **industry** guidance" and would otherwise read as
> not-for-anglers.

**3. Venue panel stamp (after shops/directions line, ~line 1002)** — one appended line in
the panel render, covering parking + phones + tide ceilings for all 8 marks + "all":
`h+='<p class="checked">Parking, phone numbers &amp; tide windows checked 26 Jul 2026 —
re-verify before travelling.</p>';`
Category-level stamp, deliberately: one per surface, not 40 inline stamps (words-first —
stats stay scannable). Panel is already JS-rendered, so no no-JS regression.

**4. Print fix (task chip, rider)** — ~4 lines of JS near the existing listeners:
`beforeprint` records every `details:not([open])` and opens them; `afterprint` re-closes
exactly those. Keep the existing `@media print{details.ref{display:block}}` /
`.dogbox` print CSS as harmless fallback. This makes CLAUDE.md's "Print opens all
<details>" line actually true.

## Acceptance criteria
- `grep -c '26 Jul 2026'` returns exactly 3 (banner, regs, venue-panel JS).
- No stamp uses "checked" for a fact that was not source-checked (see build amendment).
- Both verify links curl 200 at build time; open in new tab.
- Banner renders with JS off; legible in night mode (vars only); prints fine.
- Venue panel shows the stamp for all 8 real marks.
  ~~including "all"~~ — **amended in build:** the "all" pseudo-venue returns early with a
  badge-explainer and carries no parking, phone or tide facts, so a stamp there would
  claim facts the panel doesn't show. The header banner covers that view.
- Print: emulated `beforeprint` opens dogbox + all closed `details.ref`; `afterprint`
  restores their prior closed state; toggles user had opened stay open.
- Zero facts, badges, safety copy, or venue data changed — additions + the one regs
  sentence rewording only.

## Regression risks
- Venue-panel append must be its own `h+=` after the directions link — don't touch the
  existing string.
- specstrip untouched (banner sits after it, inside header `.wrap`).
- `.checked` must not hardcode colours (night mode).
- Print listener + existing print CSS coexist; don't remove the CSS.

## Out of scope
estimate/approximate tags (①d) · start-here router + credits (②) · tide-table links ·
per-stat inline stamps · footer rewording · any venue/regs fact changes.

## On pass (same commit)
CLAUDE.md: tick ①(c) in roadmap; note the date-literal find-replace convention; print
line in architecture is now accurate. Dismiss task chip task_fefedb94 (folded in here).

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ① on 30 Jul 2026.

Shipped: global "Page reviewed <date>" banner under the specstrip, per-venue "Venue
details reviewed <date>" line, NWIFCA + gov.uk bass-rules links in the regs box.
The same commit fixed print not expanding closed `<details>`.

The stamp-wording rule this established is load-bearing and stays in CLAUDE.md
principle 2: "reviewed <date>" means someone read the page that day; "verified against
<source> <date>" is only for facts actually re-checked against that source. Later items
leaned on it — size-limits kept its own "verified 28 Jul 2026" stamp while the page
banner stayed "reviewed 26 Jul", because the whole page was not re-reviewed.
