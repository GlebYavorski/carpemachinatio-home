# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Landing page for **carpemachinatio.com** — Gleb's personal site, a hub linking to the web services he builds. Single-file static page: pure HTML + CSS + vanilla JS in `index.html`. No build step, no dependencies, no backend.

Aesthetic: minimalist game-menu (inspired by *Deus Ex: Human Revolution*) — monochrome white/black/gray, angular clipped corners, wide letter-spacing, an animated canvas of drifting wireframe triangles and hexagons. Companion to the Telegram channel **@carpemachinatio** (https://t.me/carpemachinatio).

## How It Works (`index.html`)

- **Theming**: CSS custom properties on `:root` (dark default) with a `@media (prefers-color-scheme: light)` block. A manual toggle (top-right button) sets `html[data-theme="dark|light"]`, which overrides the system preference and persists in `localStorage` under key `theme`. The toggle dispatches a `themechange` event so the canvas recolors live.
- **Background**: `#bg` canvas. Shapes are wireframe triangles (3 sides) and hexagons (6 sides) that drift upward and wrap around edges. Shape count scales with viewport area (`makeShape`/`build`). Stroke color is read from the `--shape` CSS var via `readColor()` and refreshed on `themechange` / system change.
- **Menu**: each `.item` is an angular button (`clip-path` cuts opposite corners). Service links point to subdomains (e.g. Road Bingo → `bingo.carpemachinatio.com`). Add new services as more `.item` anchors.

## Conventions

- Keep it **single-file, zero-dependency**. No frameworks, no build tooling. If that changes, document why here first.
- Palette is **white/black/gray only** — both themes must stay monochrome. New colors need a reason.
- Both color schemes live in CSS vars; when adding a var, define it in dark `:root`, the light `@media` block, AND both `html[data-theme]` overrides.

## Deployment

Hosted on **Cloudflare as a Worker with static assets** (the newer unified "Workers & Pages" model — NOT a classic Pages project). The sibling `road_bingo` is deployed the same way, as a Worker with static assets. Git integration via **Workers Builds** connected to `GlebYavorski/carpemachinatio-home`. No GitHub Actions workflow.

- Static site: no build step, assets served from repo root.
- Push to `main` → Cloudflare auto-builds and deploys (~30s). Verified working.
- Custom domain: apex `carpemachinatio.com`, attached in the Worker's **Domains** tab.
- `www.carpemachinatio.com` → 301 redirect to apex via a zone-level Redirect Rule (wildcard `https://www.*` → `https://${1}`), backed by a proxied `www` CNAME → `carpemachinatio.com`.
- **Gotcha:** auto-deploy only works because the Cloudflare GitHub App has access to this repo. If a new repo's pushes don't deploy, check the app's repo access at github.com/settings/installations (grant "All repositories" to avoid per-repo grants).

## Zone Security Settings (Cloudflare Security Insights)

Cloudflare periodically emails "Security Insights" alerts for the zone (Moderate/Low — recommendations, not active vulnerabilities). Standing decisions on what to act on:

- **Always Use HTTPS** — enable (SSL/TLS → Edge Certificates). One zone-wide toggle covers all subdomains. Matters most for `n8n.carpemachinatio.com` (automation with webhooks/credentials), not just the static landing page.
- **HSTS** — enable *after* Always Use HTTPS is on and every subdomain confirmed serving HTTPS. Start with a short `max-age`; do NOT enable `includeSubDomains`/preload — a future subdomain without HTTPS would become unreachable and the policy is browser-cached (hard to roll back).
- **Bot Fight Mode / Block AI bots / AI Labyrinth** — intentionally left OFF. This is a public landing page announced via the @carpemachinatio Telegram channel; bot blocking can break legitimate link-preview crawlers (Telegram, Twitter, etc.).
- **Security.txt** — optional, skipped for now. Would be `/.well-known/security.txt` served from the repo if ever wanted.
