# Browser acceptance and regression tests

1. Unzip and open `index.html`; verify 41 topic links, 22 integration links, and index = 64 HTML pages.
2. Search `privacy`, filter to integration labs, then clear the query; verify correct visibility/count.
3. Mark a page complete, return to index, verify progress count increases. Export progress JSON and import it into a freshly reset index.
4. Open `labs/advanced-forms.html`, try invalid input, then valid input; verify structured FormData.
5. Open `labs/storage.html`; write and read an IndexedDB record (file:// behavior can vary).
6. Open `labs/security.html`; supply `<img src=x onerror=alert(1)>` and verify it is shown as text, never executed.
7. Open `integrations/offline-archive.html`; create, edit, search, export, reload, delete and re-import a record.
8. Open `integrations/map-notebook.html`; save valid coordinates, verify an SVG marker, export GeoJSON and check `[lon, lat]` order.
9. Run `python -m http.server 8000`, open `http://localhost:8000/`, wait for service worker to control navigation, visit any lab, disconnect network, reload the visited lab.
10. Test each page at 320px width, 200% zoom and with keyboard-only navigation; test Windows high-contrast and reduced-motion when available.
11. Test protected APIs with denied permissions; verify clear errors and no automatic requests.
12. Inspect network requests for unexpected third-party calls; verify exports, imports and updates remain local.

Static validation and JavaScript syntax checks are generated in `verification.json` by the build pipeline; manual cross-browser tests are still required. WebXR, hardware integrations, browser built-in AI, actual remote networking, full application security and screen-reader usability are *not* automatically validated by those checks.

## V2 specialty workflows to check
Every integration now provides one functional, domain-specific workbench. Check: archive backlinks, garden area scenarios, quest completion, unit-aware field surveys, evidence review, inventory priority, governance stage filtering, map distances, motif analysis, transcript export, uncertainty grouping, accessibility failure triage, privacy minimization, signed-if-available exchange manifest, Kanban transitions, paired monitoring changes, deterministic unit conversion, AI evaluation denominator, deployment evidence gating, supply expiry, skill crosswalk and publication metadata gating. Browser automation test results are summarized in verification_v2.json; a separate full accessibility, security, and cross-browser audit is still needed.
