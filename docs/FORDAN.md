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

## Session 4: The Getting-Started Guide (2026-03-03)

The foundation guide was built for you — someone already deep in the workflow. When a friend asked "how do I get started with Claude Code?", the honest answer was: the guide is too much. It's a reference manual, not an onboarding path.

So we built what was missing: `getting-started.md`. Four progressive stages that take someone from "I just installed this" to "I'm running parallel agents in worktrees." The key insight was structure — not simplification. The full guide has all the content; what beginners need is *sequencing*. Stage 1 covers install and first habits. Stage 2 introduces CLAUDE.md and project memory. Stage 3 adds the Foundation Package and automation. Stage 4 covers the advanced stuff (hooks, MCP, skills) that only matters once everything else is muscle memory.

The opinionated voice matters here. Every "start simple" guide that hedges with "you could also..." loses the reader. This one picks a path and says "do this." The cross-references to the full guide are there when someone wants the why, but the getting-started doc stands alone.

Also ran the weekly bookmark digest this session — processed 44 new X bookmarks, surfaced 7 personalized picks, and pushed them to Notion. The digest pipeline is becoming a genuine weekly habit now, which is exactly what it was designed for.

## What Transfers to Future Projects

- Convert manual external setup into a project command early (`/setup-notion-hub`) so the workflow is repeatable.
- Keep integration IDs in a dedicated tracked file, not only in chat memory.
- Treat functional completion and polish separately: get integration working first, then add public-facing refinements.
- Comprehensive references need a progressive companion. Build the deep guide first, then extract a getting-started path from it — not the other way around.
