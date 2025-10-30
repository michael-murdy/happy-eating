# Repository Guidelines

## Project Structure & Module Organization
Place all recipe and blog content under `content/`, using directories to mirror site sections (for example, `content/recipes/desserts/`). Keep custom templates in `layouts/`; use `layouts/_default` for base list and single views and `layouts/partials/` for reusable snippets. Static assets, such as photos and downloadable PDFs, belong in `static/` so they publish verbatim. Generated artifacts live in `public/` (production build output) and `resources/` (Hugo cache). Avoid editing the upstream theme in `themes/ananke/`; add overrides in the root `layouts/` or `assets/` folders instead.

## Build, Test, and Development Commands
Run `hugo server -D` to start a live-reloading preview that includes draft content. Use `hugo` for a production build; confirm the command finishes with “Total in … ms” and that the `public/` directory updates. When deploying, publish only the `public/` artifacts or let the CI pipeline run `hugo` for you.

## Coding Style & Naming Conventions
Front matter uses YAML; keep keys lowercase (`title`, `description`, `draft`) and prefer hyphenated slugs (e.g., `black-bean-tacos`). Markdown should follow 120-character soft wraps and sentence-case headings. Go template files in `layouts/` follow two-space indentation and pipe-heavy expressions should break onto new lines for readability. When adding SCSS or JS assets, mirror the naming already present in `assets/` and run them through Hugo’s pipeline instead of linking raw files.

## Testing Guidelines
There is no automated test suite; rely on Hugo’s built-in validation. Before opening a PR, run `hugo` and check for warnings about missing resources or taxonomy terms. Review the rendered pages locally to confirm links, images, and structured data render correctly. For new content types, add a sample page under `content/` and verify the matching template resolves as expected.

## Commit & Pull Request Guidelines
Commit messages follow an imperative, present-tense summary (`Add seasonal menu landing`). Group related edits and avoid mixing content and template refactors in the same commit. Pull requests should include: a concise summary, screenshots or GIFs of visual changes, links to any relevant issues, and a note on testing steps (e.g., “Ran `hugo` locally”). Tag reviewers familiar with the affected section (content vs. theme) to speed turnaround.
