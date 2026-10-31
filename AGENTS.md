# AGENTS.md — NEXARUS static sales page

## What this repo is
A single static landing page, no build step, no backend, no database.
- `index.html` — the served page. It is **fully self-contained**: all CSS, JS and images
  (Base64) are inlined. `styles.css` and `script.js` exist as editable sources but are
  **not referenced** by `index.html`, so editing them alone changes nothing on screen.
- `standalone-sales-page.html` — an all-in-one copy for pasting into a CMS (Odoo).
- External assets are only CDN/remote: Google Fonts, FontAwesome, YouTube thumbnails,
  WhatsApp links. No credentials, no API keys, no env vars required.

## Running it here
```
docker compose -f docker-compose.base44.yml up -d --build
```
- One service, `web`: `python:3.12-slim` running `python -m http.server 3000`
  with the repo bind-mounted read-only at `/srv`, published on host port 3000.
- nginx was tried first and returned 403: the checkout is root-owned with `700`
  permissions, so nginx's worker user cannot traverse it. The Python server runs as
  root and reads the bind mount fine.
- There is no live-reload dev server (there is no toolchain to reload). nginx/Python
  serve files fresh per request, so a change to `index.html` shows on refresh —
  call `reload_preview` after editing.

## Editing guidance
Most visible copy/markup lives **inside `index.html`** (inline `<style>` and `<script>`).
Grep for the text, change it there. Keep the standalone file in sync only if asked.

## How to verify
- `curl -s -o /dev/null -w '%{http_code}' http://localhost:3000/` → 200 and ~550 KB.
- Page title should be `NEXARUS | Aprenda 3 Anos de Inglês em 1 Mês...`.
- Interactive pieces (all vanilla JS in the page): sticky navbar on scroll,
  mobile menu (`#mobileToggle` → `#navLinks.open`), FAQ accordion (`.faq-item.active`),
  YouTube video modal, smooth anchor scrolling.
