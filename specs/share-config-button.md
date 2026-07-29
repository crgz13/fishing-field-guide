# Spec — share-config button

## Why (roadmap ⑤)
CLAUDE.md already says "the hash doubles as the shareable export" — but nothing on the
page tells a stranger that. Today sharing your setup means knowing to copy the address
bar after the hash appears. This makes the existing mechanism visible: one button that
hands a mate a link which opens the guide with your rod, line and mark already set.

## Change

### 1. Button in the gear deck
Static HTML in `#geardeck` after the `#savedline` row:

```html
<p class="whynote sharewrap" id="sharewrap" hidden>
  <button type="button" id="sharebtn">Share this setup</button>
  <span id="shareout"></span>
</p>
```

`hidden` is removed on JS init — the same pattern as `#norodnote`/`#savedline`, so a
JS-off page shows no dead control (words-first: nothing broken to look at).

### 2. Behaviour on click
Compose the URL — never touch the address bar:

- **Base = the canonical `<link>` href, read from the DOM** (`link[rel=canonical]`).
  One source, can't drift — and someone running the raw offline file shares a link
  that works for the recipient, which is the footer's own "latest version" logic.
- **Fragment = the exact `writeHash()` payload.** Extract the encoder into
  `hashString()` and have `writeHash()` and the button both call it (the LEADS trick:
  shared source, the two can't diverge). Carries `v/r/l` + `cw` for custom + `nr=1`.
- Fallback chain, first available wins:
  1. `navigator.share({url})` — the native share sheet (rung 3: the platform feature
     for exactly this; the audience is on phones at the water).
  2. `navigator.clipboard.writeText(url)` → on resolve, show "Copied ✓" + the URL.
  3. Neither / rejection → show the URL in the output span with "copy it yourself".

### 3. Copy under the button (one sentence, in `#shareout`'s row or title text)
"The link opens this guide with your rod, line and mark already set. Your locker and
catch log stay on this device — they have their own export files."

## Decisions (made now, at spec time)

1. **The link carries the setup hash only** — rod, line, venue, custom weights, no-rod
   flag. Locker and catch log are EXCLUDED on purpose: both already have deliberate
   JSON export/import as their transfer path, and a catch log is dates-and-places of a
   real person — it must never travel silently inside a URL pasted into a group chat.
   The copy names the exclusion so nobody assumes the link is a full backup.
2. **Share never mutates.** No `location.hash` write, no `saveProfile()`, `INIT_DONE`
   untouched. A share is a read. Clean URLs stay clean; the sender's profile and the
   address bar are byte-identical before and after a click.
3. **"Copied ✓" only when the clipboard promise resolves.** A failed copy that says
   "copied" is the false-stamp rule in miniature. The `navigator.share` path claims
   nothing at all — the OS sheet is its own confirmation, and the user may cancel it
   in a way the page can't see.
4. **The URL is always displayed** after a successful copy (and on fallback), so the
   sender can see exactly what they are sending before they paste it.
5. **Base URL is the canonical link, even locally.** Sharing from `file://` or
   localhost still emits the live https URL — a `file://` path is useless to a
   recipient and leaks the sender's directory structure.

## Acceptance criteria
- Copied string === canonical href + `#` + current `hashString()`; custom rod round-trips
  `cw=min-max`; no-rod round-trips `nr=1`. Opening the copied URL in a clean browser
  (no profile) reproduces rod/line/venue via the existing `readHash()`.
- After a click: `location.hash` unchanged, `localStorage['efg-profile']` unchanged.
- JS-off: no visible button. Print: `.sharewrap` hidden. Night mode: inherits existing
  button/whynote styling, zero new palette rules.
- `writeHash()` behaviour unchanged (still gated on `INIT_DONE`, still writes the same
  string — now via `hashString()`).
- Footer, canonical link, first-visit card untouched.

## Out of scope
QR code (canvas code or a dependency for marginal value) · shortlinks (a third-party
service on a no-accounts page) · sharing locker/log (their JSON export is the path) ·
any change to hash precedence or the recipient's first-visit flow.

## On pass (same commit)
CLAUDE.md ⑤ records: share button done + decisions above (setup-hash-only with the
privacy rationale, never-mutates, copied-only-on-resolve, canonical base even offline).

## Decisions & errata (post-build)
Moved here from CLAUDE.md's roadmap ⑤ on 30 Jul 2026.

"Share this setup" in the gear deck: canonical URL + the existing hash payload, handed
over via `navigator.share` → clipboard → visible-URL fallback.

### Decisions
- **The link carries the setup hash ONLY** (rod/line/venue/cw/nr). Locker and catch log are
  excluded on purpose: both have their own JSON export, and **a catch log is dates-and-places
  of a real person that must never travel silently in a URL pasted into a group chat.** The
  visible copy says so.
- **A share is a READ** — no `location.hash` write, no `saveProfile()`. Address bar and
  profile are byte-identical after a click.
- **"Copied ✓" only when the clipboard promise resolves** — the false-stamp rule in
  miniature. The real clipboard rejecting a synthetic click proved the guard works. The
  `navigator.share` branch claims nothing, because the OS sheet is its own confirmation and
  a cancel is invisible.
- **The base URL is the canonical `<link>`, even from `file://`** — read from the DOM so the
  two cannot drift (the LEADS trick). A local path is useless to the recipient and leaks the
  sender's directory structure. See `hosting.md`.

The encoder was extracted to `hashString()`, shared by `writeHash()` and the button.

Markup ships static with `hidden`; JS removes it at init end, so JS-off shows no dead
control (the `norodnote`/`savedline` pattern).

### Note for future `.deck` styling
`#firstvisit` is a **SECOND** runtime element with `class="deck"` (built by
`buildFirstVisit`), so a static grep for `class="deck"` finds only `#geardeck` and
undercounts. `.deck button.act` is safe today only because the first-visit buttons are
classless.
