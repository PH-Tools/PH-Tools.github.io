# PH-Tools Landing Page

Public landing page for [passivehousetools.com](https://passivehousetools.com) — the entry point for the PH-Tools open-source ecosystem.

## Architecture Overview

PH-Tools has **two web properties** sharing the same [bldgtyp design system](https://bldgtyp.github.io/branding/):

```
passivehousetools.com          ← THIS REPO (landing page)
docs.passivehousetools.com     ← PH-Tools/ph-docs (developer docs hub)
```

### How the pieces fit together

```
┌─────────────────────────────────────────────────────────────┐
│                     passivehousetools.com                    │
│                        (this repo)                          │
│                                                             │
│  Landing page, install guide, quick start, resources,       │
│  contact. Links OUT to docs and individual project repos.   │
│                                                             │
│  Tech: Astro 5, bldgtyp design tokens (CDN), GitHub Pages  │
│  Repo: PH-Tools/PH-Tools.github.io                         │
│  Deploy: GitHub Actions → actions/deploy-pages              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────────┐    ┌───────────────────────────┐  │
│  │  docs.passivehou...  │    │  ph-tools.github.io/      │  │
│  │  (PH-Tools/ph-docs)  │    │  honeybee_grasshopper_ph/ │  │
│  │                      │    │                           │  │
│  │  API docs, guides,   │    │  Redirect pages only.     │  │
│  │  LLM docs. Fetches   │    │  Old Hugo URLs redirect   │  │
│  │  from spoke repos    │    │  to passivehousetools.com  │  │
│  │  nightly.            │    │  via meta-refresh.         │  │
│  └──────────────────────┘    └───────────────────────────┘  │
│                                                             │
│  ┌──────────────────────┐    ┌───────────────────────────┐  │
│  │  ph-tools.github.io/ │    │  bldgtyp.github.io/       │  │
│  │  CarbonCheck/        │    │  branding/                │  │
│  │                      │    │                           │  │
│  │  CarbonCheck app.    │    │  Design tokens CSS/JSON   │  │
│  │  Separate repo,      │    │  imported by both sites   │  │
│  │  own GitHub Pages.   │    │  via CDN.                 │  │
│  └──────────────────────┘    └───────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Cross-links

- **Landing → Docs**: Header nav has "Docs" link → `docs.passivehousetools.com`
- **Docs → Landing**: Header has home icon → `passivehousetools.com`
- **Landing → Projects**: Each project card links to its docs spoke or repo
- **`/docs` route**: Meta-refresh redirect to `docs.passivehousetools.com`

## DNS & Hosting

**Domain**: `passivehousetools.com` — registered at **Dreamhost** (expires 2027-01-04), DNS Only plan.

**DNS records** (managed in Dreamhost panel):

| Name | Type | Value | Purpose |
|------|------|-------|---------|
| `@` | A (x4) | `185.199.108-111.153` | Apex domain → GitHub Pages |
| `www` | CNAME | `ph-tools.github.io` | www subdomain → GitHub Pages |
| `docs` | CNAME | `ph-tools.github.io` | Docs subdomain → ph-docs repo Pages |
| `@` | NS (x3) | `ns1-3.dreamhost.com` | Dreamhost nameservers (do not touch) |

**GitHub Pages config**: Source = GitHub Actions workflow, HTTPS enforced, custom domain = `passivehousetools.com`.

**No hosting plan** — Dreamhost "Shared Unlimited" was deactivated during migration. Domain is DNS Only. Do not re-enable hosting.

## Project Structure

```
PH-Tools.github.io/
├── .github/workflows/
│   └── deploy.yml              ← Build Astro + deploy to GitHub Pages
├── public/
│   ├── CNAME                   ← Custom domain (passivehousetools.com)
│   ├── favicon.svg
│   ├── robots.txt
│   └── img/
│       ├── install/            ← 7 PNGs (install guide screenshots)
│       ├── quick-start/        ← 9 PNGs + 1 SVG (tutorial screenshots)
│       └── resources/          ← 4 PNGs (video splash images)
├── src/
│   ├── components/
│   │   ├── Header.astro        ← Sticky header, nav, theme toggle, hamburger
│   │   └── Footer.astro        ← Footer links
│   ├── layouts/
│   │   └── BaseLayout.astro    ← HTML shell, theme persistence, meta tags
│   ├── pages/
│   │   ├── index.astro         ← Landing page (hero, project cards, features)
│   │   ├── install.astro       ← Install guide (migrated from Hugo)
│   │   ├── quick-start.astro   ← Quick start tutorial (migrated from Hugo)
│   │   ├── resources.astro     ← Videos & learning resources (migrated)
│   │   ├── contact.astro       ← Contact & help (migrated from Hugo)
│   │   ├── docs.astro          ← Meta-refresh redirect → docs subdomain
│   │   └── 404.astro           ← Branded 404 page
│   └── styles/
│       └── global.css          ← Design system (imports bldgtyp CDN tokens)
├── astro.config.ts             ← Astro config (static output, sitemap)
├── package.json                ← pnpm, Astro 5, @astrojs/sitemap
└── tsconfig.json
```

## Design System

Both this site and ph-docs import the same bldgtyp design tokens:

- **Tokens CSS**: `https://bldgtyp.github.io/branding/tokens/tokens.css`
- **Fonts**: Outfit (sans) + JetBrains Mono (code) via Google Fonts
- **Colors**: `--accent: #7a9424` (sage green), `--highlight: #DC2626` (red)
- **Themes**: Light (default) and dark, toggled via `data-theme` attribute, persisted in `localStorage`
- **Graph paper**: CSS background pattern using accent color at low opacity

Source: [bldgtyp/branding](https://github.com/bldgtyp/branding)

## Development

```bash
pnpm install
pnpm dev          # http://localhost:4321
pnpm build        # Static output to dist/
pnpm preview      # Preview built site
```

## Deployment

Automated via GitHub Actions on push to `main`:

1. `pnpm install && pnpm build` (Astro static build)
2. `actions/upload-pages-artifact` uploads `dist/`
3. `actions/deploy-pages` deploys to GitHub Pages

No manual deployment needed. Push to `main` and it deploys.

## Content Migration History

This site was created in April 2026 to consolidate PH-Tools web properties:

- **Before**: Landing page on Dreamhost (static HTML), docs on Hugo in `honeybee_grasshopper_ph/docs/`, API docs scattered across repos
- **After**: Landing page here (Astro on GitHub Pages), docs consolidated at `ph-docs`, old Hugo URLs redirect via meta-refresh pages

The Hugo site content (install guide, quick start, resources, contact) was migrated to Astro pages in this repo. The old `docs/` directory in `honeybee_grasshopper_ph` now contains only static redirect HTML files that forward old URLs to `passivehousetools.com`.

Full migration plan and checklist: [`honeybee_grasshopper_ph/WEBSITE_CONSOLIDATION_PLAN.md`](https://github.com/PH-Tools/honeybee_grasshopper_ph/blob/main/WEBSITE_CONSOLIDATION_PLAN.md)

## Related Repos

| Repo | Purpose | URL |
|------|---------|-----|
| [PH-Tools/ph-docs](https://github.com/PH-Tools/ph-docs) | Developer docs hub | [docs.passivehousetools.com](https://docs.passivehousetools.com) |
| [PH-Tools/honeybee_grasshopper_ph](https://github.com/PH-Tools/honeybee_grasshopper_ph) | Grasshopper plugin + redirect pages | [ph-tools.github.io/honeybee_grasshopper_ph/](https://ph-tools.github.io/honeybee_grasshopper_ph/) |
| [PH-Tools/CarbonCheck](https://github.com/PH-Tools/CarbonCheck) | Embodied carbon tool | [ph-tools.github.io/CarbonCheck/](https://ph-tools.github.io/CarbonCheck/) |
| [bldgtyp/branding](https://github.com/bldgtyp/branding) | Design tokens & components | [bldgtyp.github.io/branding/](https://bldgtyp.github.io/branding/) |
| [PH-Tools/honeybee_ph](https://github.com/PH-Tools/honeybee_ph) | Core Python data model | — |
| [PH-Tools/PHX](https://github.com/PH-Tools/PHX) | WUFI/PHPP conversion engine | — |
