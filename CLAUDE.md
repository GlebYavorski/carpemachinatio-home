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

Target: **Cloudflare Pages** connected to the GitHub repo `GlebYavorski/carpemachinatio-home` (same model as the sibling `road_bingo` repo — Pages Git integration, no GitHub Actions workflow).

- Static site: no build command, output dir = `/`.
- Push to `main` → Cloudflare auto-builds and deploys.
- Custom domain: apex `carpemachinatio.com`, configured in the Pages project's Custom Domains.
