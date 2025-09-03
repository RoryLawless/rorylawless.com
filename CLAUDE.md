# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a personal website built with Quarto, a scientific and technical publishing system. The site is deployed to Cloudflare Pages via GitHub Actions and serves as a blog and personal portfolio for Rory Lawless.

## Key Commands

### Build and Preview
```bash
quarto render        # Build the entire site to _site/ directory
quarto preview       # Start local development server with live reload
```

### Content Creation
To create a new blog post:
1. Create a subdirectory within `posts/` (e.g., `posts/my-new-post/`)
2. Add an `index.qmd` file to that directory
3. The blog homepage will automatically update to include the newest post when rendered

### Deployment
The site auto-deploys to Cloudflare Pages on pushes to main via `.github/workflows/quarto-publish.yml`. Manual deployment is also available via workflow_dispatch.

## Architecture

### Content Structure
- `index.qmd` - Homepage with listing of blog posts using custom template
- `posts/` - Blog posts, each in their own directory with `index.qmd`
- `posts/_metadata.yml` - Global metadata for all posts (freeze: true for computational output)
- `assets/template.ejs` - Custom EJS template for blog post listings (creates clean table layout)
- `assets/` - Static assets including styles, templates, and favicon
- `.well-known/` - Security-related files (security.txt, pgp-key.txt)

### Configuration Files
- `_quarto.yml` - Main Quarto project configuration
  - Site metadata, navbar, theming, and output settings
  - Uses custom SCSS theme with Lato/Playfair Display fonts
  - Renders only `*.qmd` files (excludes .md files like CLAUDE.md from site build)
  - Deploys to `_site/` directory
  - Includes post-render script to copy security files
- `assets/custom.scss` - Custom styles including navbar width constraints and typography
- `scripts/copy-security.sh` - Post-render script that copies .well-known directory to _site/
- `.gitignore` - Excludes Quarto build artifacts (`.quarto/`, `_site/`)

### Styling and Theme
- Background: #faf8f1 (cream)
- Text: #00202a (dark teal)
- Links: #005f73 (teal)
- Fonts: Lato (body), Playfair Display (navbar title)
- Custom navbar container width: 820px
- Code highlighting: Dracula theme

### GitHub Actions Workflows
1. `quarto-publish.yml` - Renders Quarto site and deploys to Cloudflare Pages
2. `claude.yml` - Claude Code PR Assistant workflow 
3. `claude-code-review.yml` - Claude Code Review workflow

## Development Notes

- Posts use freeze: true to cache computational output
- Site uses custom listing template for clean blog post display
- FontAwesome icons integrated via quarto-ext extension
- All content is in Quarto Markdown (.qmd) format
- No package.json or node dependencies - pure Quarto setup