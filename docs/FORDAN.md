# cc-foundation-guide - Learning Document

> Written for Dan (and future collaborators) to understand what was built and why.
> Updated at major milestones. Written engagingly, not like documentation.

## What Is This?

A practical operating system for building with AI coding agents. This project packages the setup logic (init + scanner), operating guidance (the foundation guide), and execution workflow (status/wrap-up commands) into one reusable baseline.

## How It Works

The project has three layers that reinforce each other:

1. Guidance layer: `cc-foundation-guide.md` and `website-notion-guide.md` define standards for setup, model strategy, context handling, and project tracking.
2. Operations layer: `CLAUDE.md`, `AGENTS.md`, and slash commands codify how sessions should run day to day.
3. Tracking layer: `project-meta.json`, `docs/changelog.md`, `docs/decisions.md`, and `docs/notion-hub-config.md` preserve state so progress survives across sessions and tools.

The recent milestone added Notion hub wiring (OAuth + databases + landing page + ID registry), which makes cross-project reporting practical instead of ad hoc.

## Technologies & Why We Chose Them

- Bash: fast automation for bootstrapping and project scanning across local repos.
- Markdown: low-friction, versionable documentation and prompts.
- JSON (`project-meta.json`): machine-readable state for scripts and dashboards.
- Notion MCP: optional external system for a shareable project hub without building custom backend infrastructure.

## Bugs, Gotchas & Lessons

- `set -u` can break associative-array workflows in Git Bash; `set -eo pipefail` is safer for portability.
- This terminal did not have the `claude` CLI available, so Notion creation had to be executed inside a Claude Code session.
- Notion publish controls are page-level. Inline or linked databases may not expose a separate publish action until opened as full pages.

## What Transfers to Future Projects

- Convert manual external setup into a project command early (`/setup-notion-hub`) so the workflow is repeatable.
- Keep integration IDs in a dedicated tracked file, not only in chat memory.
- Treat functional completion and polish separately: get integration working first, then add public-facing refinements.
