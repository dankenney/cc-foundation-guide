# Changelog

## 2026-03-03 - Getting Started Guide + Bookmark Digest Pipeline

- Created `getting-started.md` — a standalone, progressive onboarding guide for new Claude Code users (357 lines, 4 stages: First Hour, First Week, First Month, Going Further)
- Guide includes copy-paste prompts, model strategy, Foundation Package overview, init script usage, and a quick reference card
- Written in opinionated voice with personal experience tips, designed to be shareable without requiring the full foundation guide
- Committed, pushed to master, and shared via GitHub URL
- Cross-session: processed weekly X/Twitter bookmark digest in x-bookmarks-kb — 18 new KB entries, 7 personalized picks pushed to Notion
- Context: Friend needed Claude Code onboarding; the full foundation guide was too dense for a first-timer. This fills the "quick start" gap the project was missing.

## 2026-02-24 - Guide v2.1: Retrofitting, Corporate Gotchas, Command Scope
- Added "Retrofitting Existing Projects" subsection to Section 6 — documents how to add Foundation Package to projects that predate the guide
- Added "Global vs Project-Level Commands" subsection — explains `~/.claude/commands/` (global) vs project-level scope, addresses the "why isn't /wrap-up showing up?" problem
- Added "From Deployment & Corporate Environments" to Section 9 — corporate firewall, auth throttling, nested subdomain TLS, ephemeral /tmp lessons
- Added Notion upgrade note to /wrap-up template in Section 6
- Updated handoff-notion-hub.md with ROI tool (9th project in portfolio)
- Bumped version to 2.1, updated last-updated date
- Context: All additions based on real issues hit during ROI tool UAT deployment to EY corporate machines

## 2026-02-19 - Project Created
- Initialized project with Foundation Package
- Created CLAUDE.md, project-meta.json, and supporting docs
- Tech stack: bash,markdown

## 2026-02-19 - Documentation Alignment and Notion Hub Setup
- Replaced README and AGENTS placeholders with concrete setup, command, and status content.
- Added `.claude/commands/setup-notion-hub.md` to standardize Notion hub setup in Claude Code.
- Added `docs/notion-hub-config.md` as the source of truth for Notion IDs, URLs, and verification status.
- Completed Notion MCP OAuth, created Project Lab/Resources/Skills Catalog databases, and created the Dan's Lab landing page.
- Captured internal Notion URLs and landing page public URL; database public URLs remain optional follow-up.
