# cc-foundation-guide

> This file mirrors CLAUDE.md for compatibility with OpenAI Codex, Cursor, and other AI coding tools.
> Source of truth: CLAUDE.md

## Project Purpose
A living reference document that codifies best practices for building with AI coding agents (Claude Code, OpenAI Codex). Contains the init script, project scanner, workflow templates, and operational guidance.

## Tech Stack
- Bash (init-project.sh, scan-projects.sh)
- Markdown (guide documents)
- Git (version control)
- Optional: Notion MCP (for project hub sync)

## Architecture
```text
cc-foundation-guide.md     <- Main guide document (comprehensive reference)
website-notion-guide.md    <- Companion guide for personal site + Notion hub
~/scripts/init-project.sh  <- Creates new projects with Foundation Package
~/scripts/scan-projects.sh <- Scans all projects and prints health summary
```

The guide defines the Foundation Package structure. The init script implements it. The scanner reads from it. Each project's `/wrap-up` command keeps its `project-meta.json` current, which the scanner aggregates.

## Key Commands
```bash
# Create a new project
~/scripts/init-project.sh <name> ["description"] ["tech,stack"]

# Scan all projects
~/scripts/scan-projects.sh

# In Claude Code, run project commands
/status
/wrap-up
/setup-notion-hub
/push-to-notion
```

## Conventions
- Write in a direct, analytical voice focused on decisions and outcomes.
- Keep guides markdown-first and cross-link key sections with anchors.
- Keep prompts copy-paste ready in fenced code blocks.
- Treat `CLAUDE.md` as source-of-truth and keep `AGENTS.md` synchronized.
- Update `docs/changelog.md`, `docs/decisions.md`, and `project-meta.json` at session close.

## Current Status
**Phase:** Active - v2.0
**In progress:** Define personal site implementation scope and deployment plan.
**Next:** Implement Step 4: build and deploy the simple personal site linked to Dan's Lab.
**Completed:** Foundation Package structure, init script, project scanner, Notion hub databases, Dan's Lab landing page, and `/wrap-up` Notion sync automation.

## Notes
- This project also uses Claude Code. See `CLAUDE.md` for CC-specific context.
- Known gotchas and deeper operational guidance are maintained in `CLAUDE.md`.


