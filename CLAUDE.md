# cc-foundation-guide

## Project Purpose
A living reference document that codifies best practices for building with AI coding agents (Claude Code, OpenAI Codex). Contains the init script, project scanner, workflow templates, and operational guidance. This guide is its own project - it eats its own dog food.

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

# In Claude Code (project commands)
/status
/wrap-up
/setup-notion-hub
/push-to-notion

# Review the guide for staleness
# (run monthly or when it feels outdated)
```

## Conventions
- Guide is written in direct, analytical voice
- Sections reference each other via markdown anchors
- Personal experience notes marked with **\***
- Prompts are copy-paste ready (fenced code blocks)

## Current Status
**Phase:** Active - v2.0
**In progress:** Define personal site implementation scope and deployment plan.
**Next:** Implement Step 4: build and deploy the simple personal site linked to Dan's Lab.
**Completed:** Foundation Package structure, init script, project scanner, Notion hub databases, Dan's Lab landing page, and `/wrap-up` Notion sync automation.

## Gotchas
- The init script uses `date -d` which may behave differently on macOS vs Linux vs Git Bash
- The scanner uses `bc` for arithmetic - ensure it is available on the system
- On Windows/Git Bash, line endings may show CRLF warnings (harmless)
- Notion publish controls are page-level; inline databases may not show a separate publish action

## Self-Improvement
When a new best practice, tool, or workflow improvement is discovered during any project session, suggest adding it to this guide if it is broadly applicable across projects.

## Installed Skills
See global skills list in ~/.claude/CLAUDE.md


