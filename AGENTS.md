# Repository Guide

## Structure
- This is a dependency-free static site: there is no package manifest, build step, or automated test/lint configuration.
- `index.html` is the primary responsive interface. `ps4-pwa-optimized.html` and `ps4-games-optimized.html` are separate, inline-script interfaces rather than shared components.
- All three interfaces load `ps4_games_expanded.json`; preserve that relative path when moving or renaming entrypoints.
- `redirect.html` and `redirect-optimized.html` are download interstitials; `redirect-optimized.html` is the one that loads `popup-blocker.js`.
- `service-worker.js` and `gamepad-controller.js` are not currently loaded or registered by any HTML entrypoint. Do not assume offline caching or controller support is active without wiring them in.

## Local Verification
- Serve the repository over HTTP before testing, because browser `fetch`/XHR of the JSON database fails when pages are opened with `file://`:
  ```powershell
  python -m http.server 8000
  ```
  Open `http://localhost:8000/`.
- Validate database edits with:
  ```powershell
  node -e "JSON.parse(require('fs').readFileSync('ps4_games_expanded.json', 'utf8')); console.log('valid JSON')"
  ```
- Manually verify the modified entrypoint, search, database load, and redirect flow. Test PWA behavior over HTTP(S), as service workers require a secure context (or localhost).

## Deployment
- `.github/workflows/static.yml` deploys the entire repository to GitHub Pages only on pushes to `main` (or manual dispatch). There is no build artifact directory.
- Changes to `service-worker.js` should use a new `CACHE_NAME` so existing clients drop the prior cache during activation.
