# CLAUDE.md - Phase Nexa Website

## Project Overview

This is the source for [phasenexa.github.io](https://phasenexa.github.io), the public website for Phase Nexa - developer tools for European energy trading professionals. The site is built with [Retype](https://retype.com/) and deployed to GitHub Pages.

## Build & Run

```bash
# Install Retype
npm install retypeapp --global

# Local development (auto-reload)
retype start

# Production build
retype build
```

The site deploys automatically via GitHub Actions to the `retype` branch, which GitHub Pages serves.

## Repository Structure

```
phasenexa.github.io/
├── .github/workflows/
│   ├── build.yml              # Manual trigger: clone repos, update content, build, deploy
│   └── claude.yml             # @claude interaction for issues/PRs
├── _includes/
│   └── head.html              # Injects custom CSS into all pages
├── static/
│   ├── css/custom.css         # Phase Nexa theme overrides
│   └── images/logo.png        # Logo file
├── libraries/                 # Per-library documentation pages
│   ├── index.md               # Library overview
│   ├── nexa-marketdata.md
│   ├── nexa-bidkit.md
│   ├── nexa-connect.md
│   └── nexa-mcp.md
├── index.md                   # Landing page (hero, CTA)
├── getting-started.md         # Quick start guide
├── premium.md                 # Premium tiers (coming soon)
├── community.md               # Community page, logos, contributing
├── retype.yml                 # Retype project configuration
└── CLAUDE.md                  # This file
```

## Content Update Workflow

When the `build.yml` workflow runs with `update_content: true`, Claude Code is invoked to:

1. Read each sibling repo cloned into `.repos/` (nexa-marketdata, nexa-bidkit, nexa-connect, nexa-mcp, etc.)
2. Analyse their README.md, CHANGELOG.md, pyproject.toml/go.mod, source code, and tests
3. Update the corresponding `libraries/*.md` pages with current information
4. Update the landing page stats or messaging if anything material has changed
5. Commit changes back to `main` before the Retype build runs

### How to update library pages

Each library page in `libraries/` follows a consistent structure:
- YAML frontmatter with `label`, `icon`, `order`, and `description`
- Hero section with library name, one-line description, and install command
- "What it does" section with key features
- Quick start code example
- API reference highlights
- Link to full GitHub repo

When updating these pages, pull information from:
- The repo's `README.md` for description and examples
- `pyproject.toml` or `go.mod` for version and dependencies
- `CHANGELOG.md` or git tags for latest version
- `src/` or source directories for module/package structure
- Test files for usage patterns

### What NOT to change automatically

- `index.md` hero copy and CTA (marketing copy, change only if instructed)
- `premium.md` pricing or tier details
- `community.md` company logos or testimonials
- `retype.yml` configuration
- `static/css/custom.css` styling

## Writing Style

- Professional but approachable. Not corporate, not overly casual.
- Write for developers and quants who are sceptical of marketing.
- Show code first, explain second. These people want to see `pip install` before they want to read about "our vision".
- British English spelling throughout (colour, analyse, optimise, licence).
- No em-dashes. Use standard punctuation.
- No sycophantic language. No "revolutionary", "cutting-edge", "game-changing".
- Keep it honest. If a feature is coming soon, say "coming soon", don't imply it exists.

## Design & Styling

The site uses Phase Nexa's design system. Key principles:
- **Less, but better** (Dieter Rams). Every element justifies its existence.
- **Colour is functional**. Green = positive/success. Red = negative/error. Violet = action. Cyan = active. Gold = value.
- **Dark theme preferred**. The Retype dark theme is configured with Phase Nexa colours.

### Colour Reference

| Name           | Hex       | Use                            |
|----------------|-----------|--------------------------------|
| Deep Space     | `#0A1E36` | Sidebar, recessed areas        |
| Quantum Blue   | `#0E2A47` | Page background                |
| Nebula         | `#122F4D` | Cards, elevated surfaces       |
| Pulse Violet   | `#8A6CFF` | Primary action, buttons, links |
| Cyber Cyan     | `#00E5FF` | Active state, info, highlights |
| Neon Gold      | `#FFD700` | Value display, emphasis        |
| Mint           | `#4DFFC3` | Positive, success, buy         |
| Electric Coral | `#FF6B6B` | Negative, sell, decline        |
| Neon Rose      | `#FF4D6A` | Error, destructive actions     |
| Amber Flux     | `#FFAA33` | Warning, caution               |

### Retype Components

Use Retype's built-in components where appropriate:
- `!!!` callouts for warnings, tips, info
- `===` panels for grouped content
- `+++` tabs for language/platform alternatives
- `[!badge]` for version badges
- Code blocks with language identifiers

## Sibling Repositories

These are the repos Claude should analyse when updating content:

| Repository        | Language | Description                                      |
|-------------------|----------|--------------------------------------------------|
| nexa-marketdata   | Python   | Unified API client for European power market data |
| nexa-bidkit       | Python   | Day-ahead and intraday auction bid generation     |
| nexa-connect      | Go       | Exchange connectivity SDK                         |
| nexa-mcp          | Python   | MCP server for LLM clients                       |

More repositories will be added over time. The workflow dynamically discovers repos in the `phasenexa` GitHub organisation.

## CI/CD

- **build.yml**: Manually triggered. Clones sibling repos, optionally runs Claude Code to update content, builds with Retype, deploys to `retype` branch for GitHub Pages.
- **claude.yml**: Responds to `@claude` mentions in issues and PR comments.
- GitHub Pages serves from the `retype` branch.

## Important Notes

- Never introduce colours outside the Phase Nexa palette.
- All numerical data in examples should use realistic European energy market values (EUR/MWh, bidding zones like NO1, DE-LU, FR, etc.).
- Code examples must be correct and runnable. Do not fabricate API responses.
- Keep page count under 100 (Retype free tier limit, unless Community Key is configured).
