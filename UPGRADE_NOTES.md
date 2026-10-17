# Upgrade notes — advanced-features pass (September 2026)

This pass verified the V2 package end-to-end and then added the features
below. It was done by hand plus scripts (not the original generator), so it
is documented separately from `verification_v2.json`, which describes that
build's own pipeline and still applies to everything it covered.

## Verification performed on the original V2 package
- SHA-256 of every shipped file matched `SHA256SUMS.json` exactly: nothing
  was corrupted or altered before this pass began.
- All 128 inline `<script>` blocks across all 64 pages parsed with
  `node --check` (0 syntax errors).
- All 517+ local `href`/`src` references resolved to real files (0 broken
  links; the one flagged candidate was demo text inside a textarea, not an
  actual reference).
- 0 secrets, private keys, or hard-coded credentials found by pattern scan.
- 0 real third-party telemetry/analytics calls (one flagged candidate was
  the word "amplitude" used in its audio-signal sense, not the SDK).
- 0 runtime external dependencies outside three inert documentation links.
- noscript fallback, print stylesheet, `lang` attribute, viewport meta, skip
  link and a `<main>` landmark were already present on all 64/64 pages.
- Every one of the 64 pages was executed in a real DOM (jsdom + a real
  IndexedDB implementation, not a mock), with its "mark complete" control
  exercised, to catch runtime errors static checks can't see. This surfaced
  six real (pre-existing, minor) issues, fixed below.

## Fixed
- `labs/gpu.html`, `labs/images.html`, `labs/visualization.html`: each
  called `canvas.getContext('2d')` and used the result without checking it
  first. Always succeeds in a real browser, but now guarded (`if(!x)return`)
  rather than assumed.
- `labs/hosting.html`, `labs/pwa.html`, `labs/security.html`: referenced the
  bare `isSecureContext` global with no fallback if it's ever unavailable;
  now falls back to `location.protocol==='https:'`.

## Added (all 64 pages)
- A short Zero-Harm & Anti-Inversion note, as both an HTML comment near the
  top of the file and a visible line in the footer.
- Page state (completion, notes, HUD data) now persists to IndexedDB first,
  with an automatic, one-time migration of anyone's existing localStorage
  progress, and a transparent localStorage fallback/mirror if IndexedDB is
  unavailable. Verified with a real (fake) IndexedDB implementation across a
  fresh install, a legacy-data migration, and a second load reading from
  IndexedDB alone with localStorage cleared.
- A visit-streak tracker (local date comparison only) feeding the index
  page's HUD, updated from whichever page you actually open that day.
- A gentle client-side pacing guard on save/search/import controls.

## Added (index.html only)
- Animated, skippable splash screen; inert and instant under
  `prefers-reduced-motion`, never shown to no-JS visitors.
- An expanded gamification HUD: level (from existing XP), visit streak, and
  seven milestone badges, layered on the existing progress/XP mechanism.
- A constellation map: every one of the 64 pages as one star around a
  central hub, built from the page's own already-rendered listing (no extra
  fetch), color-coded by category and completion, keyboard-navigable, each
  star a real link.
- A long-form section built with a CSS-only (no JS) layered-parallax
  technique, flattened under `prefers-reduced-motion`, narrow viewports and
  print. Copy is addressed to the two audiences the rest of this project
  targets: procurement/policy readers and classroom use.
- Progress export/import now carries HUD data too (schema bumped to
  `atlas-progress/2`; old `atlas-progress/1` files still import correctly).

## Not changed / deliberately out of scope
- The 22 integrations' own record storage (each its own `localStorage`
  key) was left as-is. It already works, is internally consistent, and
  converting 22 independently-authored tools to IndexedDB safely is a
  larger, separately-verifiable piece of work than this pass; flag specific
  ones if you want that done next.
- No color/typography changes: this package's existing dark
  teal/mint/gold palette was kept rather than overwritten with a different
  house style, since nothing here asked for that and it already reads
  cleanly.
- `sw.js`'s cache name was bumped (`foster-navi-atlas-v2` →
  `foster-navi-atlas-v2-1`) purely so installed/offline users get all 64
  updated pages precached together on their next online visit, instead of
  one at a time as each is revisited. No other service-worker logic changed.

## Re-verification after the changes above
- Full corpus re-run of every check in the first section: still 0 syntax
  errors, 0 broken links, 0 secrets, 0 telemetry, 0 external runtime deps.
- All 64 pages re-executed in a real DOM with the completion checkbox
  exercised: 0 runtime errors.
- index.html specifically exercised further: search/filter, export,
  import (both schema versions), notes save, progress reset, and the
  splash-dismiss focus handoff — all behave as intended, including a race
  condition between two completion-tracking listeners that surfaced during
  this testing and was fixed (state-change is now event-driven off a
  successful save, rather than two independent listeners reading state on
  the same click).
