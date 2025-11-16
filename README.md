# pag6043.github.io
battalla
## Purpose
Give concise, actionable guidance for AI coding agents working on this small Express + static frontend project.

## Key files
- `server.js` — Express server; serves static files from `public/` and exposes `POST /guardar` which appends to `resultados.json`.
- `index.html` — frontend UI (Spanish), expects `script.js` and `styles.css` in the same public folder.
- `script.js` — client logic: loads first 151 Pokémon from PokeAPI, updates images, computes a simple battle result and POSTs to `/guardar`.
- `styles.css` — visual styles; many selectors rely on class/ID names present in `index.html`.
- `resultados.json` — simple JSON array used as persistent storage for saved battles.
- `codigo.txt` — contains notes and sample placement instructions (mentions putting files into `/public`).

## Big-picture architecture
- Single-process Express server hosting static frontend assets and a tiny JSON-backed API.
- Frontend is purely client-side JS that calls external PokeAPI and the local `/guardar` endpoint to persist results.
- Persistence is file-based: `resultados.json` is read & written synchronously in `server.js` (simple but not production-ready).

## Project-specific conventions & patterns
- UI and variable names are Spanish (e.g., `pokemon1`, `batallar`, UI copy). Preserve language when editing text unless asked.
- Static assets are expected under `public/` because `server.js` uses `express.static('public')`. If files are at repo root, prefer moving them into `public/` rather than changing the server unless intentionally refactoring.
- `resultados.json` is an array (`[]`) of battle records. New entries are appended via a push and a full overwrite of the file.
- Avoid changing DOM IDs or class names (`#pokemon1`, `#img1`, `#batallar`, `#resultado`) unless also updating `index.html` and `script.js` together.

## Important integration points
- Client → external API: `script.js` requests `https://pokeapi.co/api/v2/pokemon/{id}` for data and sprites.
- Client → local server: `fetch('/guardar', { method: 'POST', ... })` in `script.js` maps to `app.post('/guardar', ...)` in `server.js` writing to `resultados.json`.

## Running & debugging (Windows PowerShell)
1. Ensure dependencies are installed:
```
npm init -y
npm install express
```
2. Start server:
```
node server.js
```
3. Open `http://localhost:3000` in a browser.

Notes for debugging:
- If static files are 404ing, verify they are located in `public/` (e.g., `public/index.html`, `public/script.js`, `public/styles.css`).
- `server.js` uses synchronous file I/O; a long-running write or concurrent requests can block — prefer `fs.promises` if converting to async.

## Editing guidance for AI agents
- Small UI changes: update `index.html` and `styles.css` together; keep IDs and class names stable.
- If modifying how results are stored, update both `server.js` and `resultados.json` shape. Example record created by `script.js`:
```
{
  "pokemon1": "bulbasaur",
  "pokemon2": "charmander",
  "ganador": "BULBASAUR",
  "fecha": "2025-11-16T..."
}
```
- When adding new API endpoints, follow existing pattern: add route to `server.js` and use `express.json()` middleware (already enabled).
- Prefer non-blocking changes where appropriate: replace `fs.readFileSync` / `fs.writeFileSync` with `fs.promises.readFile` / `fs.promises.writeFile` if adding concurrency or tests.

## Tests / Build
- There is no test harness or bundler. Changes should be testable by running `node server.js` and using the browser. If you add dev tooling, include a minimal `README` and update this file.

## Examples to reference while coding
- Save flow: `script.js` → `fetch('/guardar', { method: 'POST', body: JSON.stringify(...) })` → `server.js` reads `resultados.json`, pushes, writes file.
- Static hosting: `app.use(express.static('public'))` — place assets accordingly.

If any area above is unclear or you want the instructions to be stricter (for example: preferred async APIs, formatting rules for `resultados.json`, or a README update), tell me which sections to expand or change.
