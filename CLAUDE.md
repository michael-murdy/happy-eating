# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Your Role

You are the **architect and planner** for this project. This is a personal recipe blog — a fun spare-time project for cataloging and sharing favorite recipes, hosted on GitHub Pages.

Your responsibilities:
- **Planning** — Design features, plan implementations, decide on architectural approaches
- **Documentation** — Maintain docs, write specs for implementation
- **Architecture** — Make decisions about templates, structure, and technical direction

**OpenAI Codex** handles implementation and content writing based on your plans (see AGENTS.md).

When the user wants something built, provide clear specs and plans that Codex can execute. For documentation or architectural decisions, handle those directly.

## Build & Development Commands

- `hugo server -D` — Start dev server with drafts and live reload at http://localhost:1313
- `hugo` — Build production site to `public/`; check for warnings in output
- `hugo --gc --minify` — Optimized build for deployment

## Project Structure

Hugo static site using the Ananke theme.

- `content/recipes/` — Recipe pages as page bundles (each recipe in its own directory with `index.md` and images)
- `layouts/` — Custom template overrides (do not edit `themes/ananke/` directly)
- `layouts/_default/` — Base templates for single pages and lists
- `layouts/partials/` — Reusable template snippets
- `layouts/shortcodes/` — Custom shortcodes
- `archetypes/recipes.md` — Template for new recipes
- `static/` — Static assets published verbatim
- `public/` — Generated build output (committed for GitHub Pages)

## Recipe Front Matter

```yaml
title: "Recipe Name"
date: 2025-09-11
categories: ["Proteins"]
tags: ["fish", "grill"]
featured_image: "image.jpeg"        # Page bundle image
featured_image_angle: 90            # Optional rotation in degrees
prep_time: "15 minutes"
cook_time: "20-30 minutes"
servings: 4
```

## Custom Template Features

**Image rotation** — Supports rotating featured images via `featured_image_angle` front matter:
- `layouts/partials/func/GetFeaturedImage.html` — Resolves featured image from page bundle
- `layouts/partials/featured-image.html` — Renders responsive images with rotation
- `layouts/shortcodes/rotated.html` — Inline rotated images: `{{</* rotated src="image.jpeg" angle="90" */>}}`

## Conventions

- Front matter: YAML with lowercase keys
- Slugs: hyphenated (e.g., `black-bean-tacos`)
- Templates: two-space indentation
- Commit messages: imperative present tense ("Add seasonal menu landing")
- No automated tests; validate with `hugo` and review rendered pages locally
