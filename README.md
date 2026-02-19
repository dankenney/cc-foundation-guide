# cc-foundation-guide

Comprehensive reference for building with AI coding agents - project setup, context management, model strategy, and quality workflows.

## Getting Started

1. Read `cc-foundation-guide.md` for the core workflow and Foundation Package standards.
2. Read `website-notion-guide.md` for the Notion hub + personal site pipeline.
3. Review `CLAUDE.md` and `AGENTS.md` for project context, conventions, and command references.
4. Use the scripts below from your shell to bootstrap and monitor projects.

## Key Commands

```bash
# Create a new project with the Foundation Package
~/scripts/init-project.sh <name> ["description"] ["tech,stack"]

# Scan all projects and show health summary
~/scripts/scan-projects.sh

# In Claude Code, run project maintenance prompts
/status
/wrap-up
/setup-notion-hub
/push-to-notion
```

## Project Structure

- `cc-foundation-guide.md`: Main reference guide.
- `website-notion-guide.md`: Companion guide for Notion + personal site setup.
- `CLAUDE.md`: Claude Code source-of-truth project context.
- `AGENTS.md`: Cross-tool mirror for Codex/Cursor/Zed style agents.
- `docs/`: Changelog, decisions, learning doc, and Notion handoff notes.
- `docs/notion-hub-config.md`: Notion database/page IDs, URLs, and setup checklist.

## Current Status

- Phase: Active - v2.0
- In progress: Define personal site implementation scope and deployment plan
- Next: Implement Step 4: build and deploy the simple personal site linked to Dan's Lab

## Tech Stack

bash,markdown


