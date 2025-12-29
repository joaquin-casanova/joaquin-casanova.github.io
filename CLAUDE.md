# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal blog/portfolio for Joaquin Casanova, built with Hugo and the PaperMod theme. Deployed to GitHub Pages via GitHub Actions.

## Development Commands

```bash
# Run local development server (http://localhost:1313/)
hugo server

# Build site for production
hugo --minify

# Create new blog post
hugo new posts/my-post-title.md

# Create new page
hugo new my-page.md

# Update PaperMod theme to latest
git submodule update --remote --merge
```

## Prerequisites

- Hugo Extended v0.146.0+ (v0.148.0 used in CI)
- Git (for theme submodule)

## Architecture

**Theme**: PaperMod (Git submodule at `themes/PaperMod/`)

**Content Structure**:
- `content/posts/` - Blog posts (data engineering, MLOps topics)
- `content/about.md` - About page
- `content/archives.md` - Archive page (uses special layout)

**Customizations**:
- `layouts/partials/comments.html` - Disqus integration
- `layouts/shortcodes/iframe.html` - Custom iframe shortcode: `{{< iframe "url" "width" "height" >}}`
- `archetypes/Page.md` - Extended page template with all PaperMod options

**Configuration** (`config.yml`):
- Dark theme by default, toggle enabled
- Syntax highlighting: Chroma with `github-dark` style, line numbers enabled
- Comments, sharing, TOC, reading time all enabled
- Fuse.js search configured

## Deployment

Automatic via GitHub Actions on push to `main` (excluding README.md changes). The workflow:
1. Initializes and updates theme submodule
2. Builds with `hugo --minify`
3. Deploys `public/` to GitHub Pages
