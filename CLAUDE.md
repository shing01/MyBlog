# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a blog built with the **Retypeset** theme — a static blog theme based on Astro with a focus on typography and multilingual support. It uses pnpm as the package manager.

## Commands

```bash
pnpm dev          # Type-check then start dev server
pnpm build        # Type-check, build, then apply LQIP (low-quality image placeholders)
pnpm preview      # Preview production build
pnpm lint         # ESLint check (uses @antfu/eslint-config)
pnpm lint:fix     # ESLint auto-fix
pnpm new-post     # Scaffold a new blog post
pnpm format-posts # Format post markdown files
pnpm update-theme # Pull upstream theme updates
```

## Architecture

### Configuration

- `src/config.ts` — Central theme configuration (site info, colors, i18n locales, comments, SEO, footer). This is the main file users edit to customize the blog.
- `uno.config.ts` — UnoCSS config. Theme colors from `src/config.ts` feed into UnoCSS presets. Defines custom font families, shortcuts (`uno-desktop-column`, `uno-decorative-line`, `uno-round-border`), and a `cjk:` variant for CJK-specific styles.
- `astro.config.ts` — Astro config with MDX, Partytown, Sitemap, Compress integrations. Markdown pipeline uses custom remark/rehype plugins from `src/plugins/`.

### i18n System

The blog supports multiple languages (de, en, es, fr, ja, ko, pl, pt, ru, zh, zh-tw). Key files:

- `src/config.ts` exports `defaultLocale`, `moreLocales`, `allLocales`
- `src/i18n/config.ts` — Language map and comment system locale maps
- `src/i18n/ui.ts` — UI string translations per language
- `src/i18n/path.ts` — URL path generation with language prefixes
- `src/i18n/lang.ts` — Language detection and routing helpers

The default locale gets no URL prefix; other locales are prefixed (e.g., `/en/posts/...`).

### Content

- Posts go in `src/content/posts/` as `.md` or `.mdx` files
- About pages go in `src/content/about/`
- Content schema defined in `src/content.config.ts` — posts require `title` and `published`; optional fields include `tags`, `draft`, `pin`, `toc`, `lang`, `abbrlink`
- Posts with `lang: ''` (or omitted) are "universal" — shown in all languages
- Posts with a specific `lang` value only appear for that language
- Post slugs use `abbrlink` if set, otherwise the file `id`

### Routing

Pages use Astro's `[...lang]` catch-all pattern in `src/pages/`:
- `[...lang]/index.astro` — Home (pinned + posts by year)
- `[...lang]/posts/[slug].astro` — Individual post
- `[...lang]/tags/` — Tag listing and per-tag pages
- `[...lang]/about.astro` — About page
- `[...lang]/atom.xml.ts`, `rss.xml.ts` — Feed generation
- `og/[...image].ts` — Open Graph image generation

### Markdown Pipeline

Custom plugins in `src/plugins/` (all `.mjs`):
- Remark: reading time calculation, container/leaf directives
- Rehype: code copy buttons, external link handling, heading anchors, image processing

### Styling

- `src/styles/` — CSS files for fonts, global styles, markdown, comments, transitions, LQIP
- UnoCSS handles utility classes; the Vite plugin in `astro.config.ts` rewrites font URLs with the configured base path

### Path Alias

`@/*` maps to `src/*` (configured in `tsconfig.json`).

### Git Hooks

Pre-commit hook via `simple-git-hooks` runs `lint-staged` (ESLint fix on `.js`, `.mjs`, `.ts`, `.astro` files).
