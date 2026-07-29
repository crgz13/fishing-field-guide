# Spec — hosting (roadmap ③)

Written retroactively on 30 Jul 2026. ③ shipped before the spec/build/review loop
covered it, so its record lived only in CLAUDE.md's roadmap. This file is the
destination for that narrative — it documents shipped work, it is not a plan.

## Goal
Give the guide a stable URL, so localStorage keys (`efg-profile`, `efg-locker`,
`efg-log`) have a stable origin and a shared link resolves for a stranger.

## What shipped
- GitHub Pages: <https://crgz13.github.io/fishing-field-guide/>
- `index.html` redirect + `README.md` (both present since the hosting baseline).
- A canonical `<link>` in the head of `fishing-field-guide.html`.
- A footer pointer naming the live URL as **visible text**, so it survives print and
  offline — the raw file is now the offline fallback, and a fallback that cannot name
  its own origin is a dead end.

## Decisions
- **localStorage is stabilised by the hosted URL.** Before hosting, every `file://`
  path was its own origin, so a locker did not survive being moved.
- **The canonical `<link>` is the single source of the base URL.** The share-config
  button (⑤) reads it from the DOM rather than hardcoding a second copy — the same
  no-drift discipline as LEADS ↔ catalogue. It is read even from `file://`, because a
  local path is useless to a recipient and leaks the sender's directory structure.

## Out of scope
Custom domain · analytics · a build step · any change to the single-file constraint.
