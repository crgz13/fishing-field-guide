# Spec — redesign: instrument cards ("the almanac and its instruments")

## Why
Today every box on the page — prose figures, danger cards, and interactive engines —
is the same flat white rectangle with a 1.5px ink border. Nothing tells a stranger
"this part computes for you." Direction agreed from the prototype
(`redesign-mockup.html`, untracked): **two material languages**. Printed matter stays
flat on the paper. Engines become raised instrument cards. Contrast is the point:
lifting the engines makes the flat red safety box the loudest print on the page.

## Change

### 1. The `.inst` card language (CSS only)
A card = graph-paper surface + 2px ink border + hard offset shadow + an ink index tab.

- Surface: two 14px `linear-gradient` grids over `var(--card)` (`#fdfefb`, a hair
  warmer than the figures' `#fff` — the two materials differ under the ink).
- Elevation: `box-shadow:7px 7px 0 var(--shadow)` (`rgba(28,43,54,.16)`) — a hard
  riso-print offset, not a soft app shadow.
- **Tab and note are CSS pseudo-elements reading attributes**:
  `.inst::before{content:'✛ ' attr(data-tab)}` (ink bar, top-left, Archivo condensed
  caps) and `.inst[data-note]::after{content:attr(data-note)}` (small teal line,
  top-right; drops into the flow under 560px — the prototype's mobile fix).
  Attributes survive `innerHTML` rebuilds — **#vpanel is rebuilt whole by
  `renderPanel()` per venue, so a child-element tab would be wiped**. Zero JS changes.
- New CSS vars on `:root`: `--card`, `--grid`, `--shadow`, `--mono`;
  `body.night` flips them (`#140708` / `rgba(224,61,61,.10)` / `rgba(224,61,61,.22)`).
  Night tab text `#0a0405` on the red tab bar. No SVG attribute selector changes.

### 2. Who gets a card (closed list — nothing else)
| Container | data-tab | data-note |
|---|---|---|
| `#geardeck` (.deck) | `Gear` | `works these numbers out from your rod` |
| `#vpanel` | `Mark` | `changes with the chip you pick` |
| `.mapbox` + `#livemap` wrapper | `Map` | — (pin caveat already lives in the caption) |
| Weather `details.ref` | `Weather` | `judges comfort — never safety` |
| Locker `details.ref` | `Locker` | `what's in the bag, not what you're fishing today` |
| Catch log `details.ref` | `Catch log` | `your record — it never changes the bands` |
| `.rigcheck` (DO) | `Rig check` | `tap a dot for the why` |

The three engine `details.ref` get `class="ref inst"`: closed = slim raised bar with
the tab; open = full card. The other four `details.ref` (bait alternatives,
troubleshooting, glossary, sources) are reference reading — untouched.
`#firstvisit` inherits the surface via `.deck` styling but gets **no tab** (a card
that dies after two taps is not an instrument; adding a tab means adding runtime JS).

Every data-note above is a mechanical description or an existing stance restated
verbatim in kind ("judges comfort — never safety" is the weather item's own rule;
"never changes the bands" is the catch-log decision). **No note may make a new claim
about fish, venues, or safety** — notes label the tool, not the world.

### 3. Readouts go mono
`Spline Sans Mono` appended to the existing Google Fonts link, stack
`var(--mono)`: `'Spline Sans Mono',ui-monospace,monospace`. Applied to: `.derived`
values, `.vstats` values, the sun line, `.chain .val`, the wx verdict line, and
`.specstrip b` (the plate is `renderGear()` output — a readout that happens to live
in the header). Offline degradation identical in kind to Archivo/Public Sans today:
no connection → system fallback, layout intact.

### 4. Controls get physical press states
`.chips button` and `.mapjump button`: hard shadow at rest, `translate(-1px,-1px)`
+ larger shadow on hover, `translate(2px,2px)` + no shadow on `:active` and
`.active` — the selected chip reads pushed-in. Behind the existing
`prefers-reduced-motion` respect. Router anchors get the hover-lift.

### 5. Print
Cards print flat: one print rule strips `box-shadow` and the grid background from
`.inst`; borders and tab labels print (ink on paper). All existing print behaviour
untouched: details still open, chain still hidden, `#sharewrap`/`#wxctl`/pickers
still hidden.

## Decisions (made now, at spec time)

1. **Pseudo-element tabs, not child elements** — `renderPanel()` rebuilds `#vpanel`
   innerHTML per venue; attributes survive, children don't. Zero JS diff: the whole
   item is CSS + attributes on existing tags.
2. **No entrance animation.** The prototype's rise-in is dropped: this is a reading
   document, the cards are furniture, and an animation on aria-live containers is
   risk for zero reading value. Micro-states (hover/press) only.
3. **The calendar stays printed matter** even though the ▴ overlay is interactive —
   it is a table you read, and flattening it into the card language would blur the
   provenance line the estimate tag draws. Same for the rig-chain SVG diagram
   (a figure), the size-limits table, and every danger card: flat.
4. **Reference `details.ref` stay flat** — the instrument treatment marks *computation*,
   not *collapsibility*. Restyling all seven would erase the distinction the redesign
   exists to draw.
5. **Notes label the tool, not the world** — the data-note copy table above is
   exhaustive; any future note that states a fact about fishing needs a badge and a
   source like any other claim.
6. **Header bathymetric contours: severable.** One decorative inline-SVG background
   (teal ~.18 opacity paths + soundings) behind the header, `aria-hidden`,
   dimmed to .25 opacity at night, absent in print. If review finds it fights the
   words-first rule, it is dropped without touching the cards.
7. **`.deck button.act` styling note from ⑤ stands**: `#firstvisit` is a second
   runtime `.deck`; card styling applies to it by class, tab styling must not
   (no data-tab attribute = no tab — the selector is attribute-gated by design).

## Acceptance criteria
- **Zero JS diff** (the `<script>` block is byte-identical). The content diff outside
  CSS is only: `inst` class tokens + `data-tab`/`data-note` attributes + the fonts
  href + (if kept) the header SVG.
- Exactly the seven containers listed get the card; the four reference `details.ref`
  and a sampled danger card / figure / calendar table have byte-identical computed
  border, background, and box-shadow before vs after.
- No new claims: every `data-note` string matches the table in §2 verbatim.
- Four surfaces: JS-off (cards render statically, tabs visible, nothing dead);
  offline `file://` (fonts fall back, layout intact); night (vars flip, inputs stay
  dark, SVG recolouring untouched, tab legible); print (no shadow, no grid, details
  open, existing hides still hold).
- Badges, dates, safety copy, aria attributes, listeners: untouched.
- Single file; size increase ≤ ~7KB.

## Out of scope
Restyling the prototype's illustrative content into the page · any copy change beyond
the §2 note table · new fonts beyond the one mono · touching `paintRig`/`SEGWIRED`/
listeners · the prototype file itself (stays untracked, or is deleted on ship —
decide at review).

## On pass (same commit)
CLAUDE.md gains a design-language paragraph: two material languages, the closed
instrument list, pseudo-element-tabs-because-renderPanel-rebuilds, notes-label-the-
tool-not-the-world, and the severability of the contours.

## Decisions & errata (post-build)

- **Six instruments shipped, not seven — the map is deliberately not carded.**
  Leaflet's own `.leaflet-container{overflow:hidden}` clips a tab that sticks above
  the box, both inner top corners already hold controls (zoom left, layers right),
  and the only clean alternative — a new wrapper div — was outside the approved diff
  surface. Carding `.mapbox` alone was rejected too: that class is the *hidden
  offline fallback*, so it would card the invisible thing and not the visible one.
- **Build caught its own night bug:** `--card`/`--grid`/`--shadow` were declared on
  `:root` but not flipped in `body.night` — night cards kept the day surface until
  the vars were added to the night block.
- **The note breakpoint is 1180px, not the prototype's 560px.** The fixed `#nightbtn`
  sits at the viewport's top-right and covers the floating note on every viewport
  under ~1163px (measured, not guessed). Below 1180 the note drops into the card's
  flow. Its night background mask is scoped to `min-width:1181px` — in flow mode it
  painted a full-width bar across the card.
- **Margins are `margin-top`/`margin-bottom`, never the shorthand:** `.rigcheck` is
  also `.wrap`, whose `margin:0 auto` centres it — the shorthand would kill that.
- `.chain .val` in the spec table is `.chain .lfix` in the real markup. Selects are
  deliberately not mono: §3 says *readouts* go mono, and a control is not a readout.
- **The contours shipped without the prototype's fake depth numbers** — an invented
  sounding is an invented figure, and silent beats invented applies to decoration
  too. Review corrected the code comment that still claimed soundings.
- **Print colour caveat, assigned to the print one-pager item:** the tab is light
  text on an ink background, exactly like the existing `th`, `.watch` and `.safety`
  styles — browsers default to not printing backgrounds, so all of these print
  light-on-white today. Pre-existing class of issue; the print stylesheet item must
  settle it (`print-color-adjust` or dark-text fallbacks).
- **a11y note:** pseudo-element text is exposed to screen readers, so the tab and
  note are read as part of each card — acceptable because the strings are genuine
  labels ("Mark — changes with the chip you pick"). If the ✛ glyph or notes prove
  noisy in AT, the hardening is the double declaration `content:'✛ ' attr(data-tab)`
  then `content:'✛ ' attr(data-tab) / ''` (alt-text syntax, progressive).
- **Prototype disposition (review's call per Out-of-scope):** `redesign-mockup.html`
  stays untracked — commit baa1f5e explicitly wants the impeccable design lint to
  keep speaking on that file. Delete it once the shipped language stops changing.
- The pre-existing rod-select overflow in the gear deck (~74px past the content box
  on mobile, visible now the card has a real border) was spun off as its own task —
  one item per commit covers defect fixes too.
