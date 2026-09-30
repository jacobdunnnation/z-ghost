# AGENTS.md — Base44 Dev Environment

## Project Overview
GhostLink is a **static, client-side-only** personal portal (HTML/CSS/JS). No build step, no backend, no package manager. The main app is `ghostlinksinglefile.html` — a self-contained ~427KB HTML file with everything bundled inline.

## How It Runs
- Served by **nginx:alpine** via `docker-compose.base44.yml`, bind-mounting the repo root read-only at `/app`.
- nginx config: `nginx.base44.conf` (serves `ghostlinksinglefile.html` as the index at `/`).
- Web entry point: **host port 3000**.
- No live-reload dev server (plain static files). Edits to HTML are visible on browser refresh; call `reload_preview` after changes if needed.

## Key Files
- `ghostlinksinglefile.html` — the complete single-file app (primary entry point).
- `src/index.html` — modular source version that loads sub-pages (`src/games.html`, `src/ai.html`, `src/proxy.html`, etc.) into iframes.
- `assets/` — logos, game data JSON, customizable theme HTML.
- `proxy/` — proxy browser files (sw.js, bareworker.js).
- `self hosting/` — minimal loader that fetches the single-file from jsDelivr CDN.

## External Dependencies (runtime, client-side)
The app loads CDN scripts at runtime (Supabase JS, Scramjet proxy, BareMux, Font Awesome, Groq AI). These are fetched by the browser from CDNs — no server-side credentials needed. The AI terminal uses cloud-managed rotating Groq API keys by default (no setup required), with optional custom key support.

## Setup
```bash
docker compose -f docker-compose.base44.yml up -d
```
Verify: `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/` → `200`

## Notes
- The repo root needed `chmod 755` so nginx's worker process can traverse the bind mount.
- Healthcheck uses `127.0.0.1` (not `localhost`) because nginx only listens on IPv4.
