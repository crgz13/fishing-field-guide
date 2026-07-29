# Spec — ⑤ "should I go" weather check

## What it judges — and what it must never judge
Wind comfort for a light-gear shore session. **It never judges safety.** The verdict
words are "kind / workable / hard work" — never "safe", "fine to go", or "don't go" —
and a standing sentence travels with the box: *"Weather is the comfort axis. Tides are
the safety axis — nothing here overrides a cut-off warning, and this page deliberately
carries no live tide data (wrong tide data is worse than a link)."* A green weather
verdict beside a Duddon mark must not read as permission.

## UI — words first
Closed `details.ref` "Weather check" in PLAN, directly after `#vpanel`. Its **band table
is static HTML** — readable with zero JS and zero network:
under 10 mph = kind, light-rod weather · 10–20 = workable, casting and bite detection
suffer · over 20 (or gusts over 28) = hard work on light gear. Provenance sentence on
the table: *"Rule-of-thumb bands, synthesised for this guide — not measurements, not
venue data."* JS adds: native date input (default today, independent of the sun picker,
not persisted), a **"fetch forecast" button — the only thing that ever touches the
network; zero requests on page load** — and manual entry (wind mph, gust, optional
8-point direction) that is always present, not just offline, so any forecast app's
number drops straight into the same bands. Venue 'all' → "pick a mark". Result line:
band + *"wind 12–18 mph W, gusts to 25, between first light and last light"* + "Open-
Meteo, fetched 14:32" stamp. Verdict rendering reuses the g/y/r idiom.

## Data
Open-Meteo forecast API (no key), `wind_speed_unit=mph`, hourly wind/gust/direction for
the chosen date; summarised **only between first light and last light** — reusing
`sunCalc`, because 3am wind is not session wind. Marine API wave height is **displayed
as a fact, judged by no rule** ("waves ~1.2 m"); if the marine point has no data the
line is silently absent. Each venue gains a `facing` bearing — read off satellite
imagery at build time, same class as the pin coords (map-read, approximate) — used only
to gloss direction: within ±60° of facing = "blowing onshore — expect chop and harder
casting"; reciprocal = "offshore — flatter than the number suggests"; else along-shore.
The gloss never moves the band. Nothing cached; nothing written to storage or hash.

## Failure modes
Fetch error/timeout/malformed JSON → one visible message ("couldn't reach the forecast
— type the wind in from any app") and the manual fields carry on. Manual path shares
the exact judgment code with the fetched path, so offline behaviour can't drift.

## Acceptance criteria
- Band table + both standing sentences readable JS-off; grep proves "safe" never
  appears in the feature's copy.
- Zero network requests until the button is pressed (performance entries).
- Fetched Hodbarrow result: daylight bounds match the sun line's times for that date;
  spot-check one hour's mph against the raw API response by hand.
- Manual 15 mph → workable; 25 → hard work; 9 → kind; gust 30 alone → hard work.
- Offline: button fails visibly, manual works, nothing else breaks.
- Waves line absent for an upper-estuary mark that the marine API can't serve.
- Storage, hash, chip sweep: untouched. Night/print: table legible, buttons print-hidden,
  a fetched result prints as rendered.

## Out of scope
Tide data (rejected list) · rain/temperature · swell period · caching or auto-refresh ·
"best day this week" ranking · storing forecasts · Beaufort jargon · moving a band by
direction (gloss only).

## On pass (same commit)
CLAUDE.md: tick "should I go"; record — comfort-not-safety wording rule, the mandatory
tide sentence, bands synthesised + labelled, `facing` bearings are map-read approximate
data, fetch is button-only, waves fact-only.

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

Closed `details.ref` in PLAN, after `#vpanel`.

### It judges comfort, never safety
Verdict vocabulary is exactly **kind / workable / hard work** — never "safe", never "go".
The mandatory tide sentence — weather is the comfort axis, tides are the safety axis, no
live tide data on purpose — is static HTML beside a static band table
(<10 / 10–20 / >20 mph, gusts >28) carrying the "rule-of-thumb bands, synthesised"
provenance line. All of it readable JS-off.

### Fetch is button-only
Zero requests on page load, and a full chip sweep fires none. Open-Meteo — the first live
API beyond map tiles, approved 28 Jul 2026. Coords-in-URL is fine here: public venue
data, not personal. Manual entry is always present and shares `wxBand()` with the fetched
path, so offline cannot drift.

### The UTC trap
The forecast is requested with `timezone=UTC` and sliced in UTC, because `sunCalc`
returns UTC minutes — format-only-local. Getting this wrong shifts the daylight window an
hour all summer **while looking plausible**. Banded on the windiest daylight hour, and the
copy says so.

Waves are a fact judged by no rule, absent where the marine grid is null (West Bank +
Pickerings verified all-null). `WXGEN` is a generation counter that stops a late wave
response landing under a newer verdict. `wxClear()` in `select()` drops a verdict on venue
change — it was fetched for one mark and does not travel — and the fetched line names its
mark. An out-of-range date (HTTP 400) gets its own message; Open-Meteo reaches about a
fortnight.

### APPROVED DEVIATION — the onshore/offshore gloss was dropped
This supersedes the "On pass" line above, which expected a `facing` field.

OSM coastline normals were computed for all 8 marks (Overpass, seaward = tangent+90°):
right at 6, **WRONG at two** — New Brighton (101° E, a promontory corner; truth ≈ N/NW)
and West Bank (358° N; the north bank must face S). A wrong gloss says "offshore" into an
onshore blow, so **no `facing` field exists** and wind direction renders as a plain fact
("6–17 mph WSW"). Decision A again: silent beats invented.

Any future attempt must verify bearings per-mark against imagery — **the two known-bad
ones first**.
