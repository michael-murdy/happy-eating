# Happy Eating
Static recipe site powered by Hugo and the Ananke theme. Content lives in `content/` and publishes as a fully static site.

## Prerequisites
- Install the [Hugo extended binary](https://gohugo.io/getting-started/installing/) v0.118+.
- Clone this repo and run commands from the project root.

## Run Locally
- `hugo server -D` – start the dev server with draft content and live reload at `http://localhost:1313`.

## Build
- `hugo` – generate the production site into `public/`. Check the terminal output for warnings and confirm the command ends with “Total in … ms”.

## Deploy
1. Run `hugo --gc --minify` to build optimized output.
2. Commit the updated `public/` folder to the branch/target used by GitHub Pages (e.g., `gh-pages`) or let your CI pipeline publish `public/`.
