# Repository Guidelines

This is a personal recipe blog hosted on GitHub Pages — a fun spare-time project for cataloging and sharing favorite recipes.

## Agent Roles

**OpenAI Codex** — Implementer and content writer. You handle:
- Writing recipe markdown files from content provided by the user
- Implementing features and templates based on plans/specs from Claude
- Making code changes according to architectural decisions already made

**Claude** — Architect and planner (see CLAUDE.md). Handles planning, documentation, and architectural decisions.

When implementing, follow the specs and plans provided. If something is unclear or you think the plan needs adjustment, flag it rather than improvising.

## Project Structure

Place all recipe content under `content/recipes/` as page bundles (directory with `index.md` plus images). Keep custom templates in `layouts/`; use `layouts/_default` for base views and `layouts/partials/` for reusable snippets. Static assets belong in `static/`. Avoid editing `themes/ananke/`; add overrides in root `layouts/` or `assets/` instead.

## Build Commands

- `hugo server -D` — Live-reloading preview with drafts
- `hugo` — Production build to `public/`

## Writing Recipes

Use the archetype: `hugo new recipes/my-recipe-name/index.md`

Front matter template:
```yaml
title: "Recipe Name"
date: 2025-01-18
categories: ["Category"]
tags: ["tag1", "tag2"]
featured_image: "photo.jpeg"
featured_image_angle: 0
prep_time: "X minutes"
cook_time: "X minutes"
servings: X
```

Recipe structure:
- `## Ingredients` — Bulleted list
- `## Preparation` or `## Instructions` — Numbered steps
- `## Serving` — Optional serving suggestions
- Use `> **Tip:**` blockquotes for helpful notes

Place recipe photos in the same directory as `index.md` (page bundle pattern).

## Style Conventions

- YAML front matter with lowercase keys
- Hyphenated slugs (e.g., `black-bean-tacos`)
- Sentence-case headings
- 120-character soft wrap for markdown
- Two-space indentation in templates
- Commit messages: imperative present tense ("Add grilled salmon recipe")

## Before Committing

Run `hugo` and check for warnings. Review rendered pages locally to confirm images and links work.
