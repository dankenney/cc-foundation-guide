# Changelog

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
