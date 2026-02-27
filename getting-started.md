# Getting Started with Claude Code

### A no-fluff guide from zero to productive

> **Who this is for:** You just got access to Claude Code and want to be effective fast.
> **How to use it:** Work through the stages in order. Each one builds on the last. Don't skip ahead — the habits in Stage 1 matter more than the automation in Stage 3.
> **For deep dives:** This guide links to [cc-foundation-guide.md](cc-foundation-guide.md) where relevant.

---

## Stage 1: First Hour

### Install

**VS Code (recommended for building software):**
1. Install the [Claude Code extension](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) from the VS Code marketplace
2. Open a terminal in VS Code — Claude Code runs in the terminal, not a chat sidebar
3. Type `claude` to start a session

**Standalone CLI (if you prefer a terminal):**
```bash
npm install -g @anthropic-ai/claude-code
```
Then `cd` into any project folder and type `claude`.

**Claude Code Desktop (for research and analysis):**
Download from [claude.ai/download](https://claude.ai/download). Better for iterative research, document work, and analysis — not for writing code.

Pick one interface to start. You can always switch later.

### The One Thing That Matters: CLAUDE.md

Every project should have a `CLAUDE.md` file in its root. Claude reads this automatically at the start of every session. Without it, Claude has no memory between sessions — it starts cold every time.

A good CLAUDE.md has:

```markdown
# my-project

## Project Purpose
What this is and why it exists (1-2 sentences).

## Tech Stack
Languages, frameworks, key dependencies.

## Architecture
How the pieces connect (keep it high-level).

## Key Commands
Exact commands for build, test, deploy — Claude will copy-paste these.

## Conventions
Naming patterns, file organization, code style.

## Current Status
What phase you're in, what's in progress, what's next.

## Gotchas
Things that will trip Claude up if it doesn't know them.
```

**Don't write this alone.** Your first session in a new project should be Claude interviewing you:

```
I just started this project. Interview me about what I'm building — ask
questions to understand the goal, users, constraints, and technical
requirements. Then write a CLAUDE.md based on what you learn.
```

This single habit — having Claude write CLAUDE.md by interviewing you — improved output quality more than any other technique. Spend 30 minutes on it. It pays for itself within the first session.

### Model Strategy in One Sentence

**Opus plans. Sonnet builds.**

Claude Code has multiple models:
- **Opus** — Deeper reasoning. Use for planning, architecture, debugging walls, research.
- **Sonnet** — Faster execution. Use for writing code, refactoring, tests, straightforward tasks.
- **Haiku** — Quickest. Use for trivial edits, formatting, simple lookups.

You don't need to switch models manually. Use **Plan Mode**:

| Action | What happens |
|--------|-------------|
| `Shift+Tab` | Enter Plan Mode — Claude switches to Opus, focuses on thinking |
| Type normally | Exit Plan Mode — Claude uses Sonnet, focuses on doing |

Start every non-trivial task in Plan Mode. Let Claude think before it builds.

### Three Habits to Start With

1. **End planning prompts with:** `"Any questions before you start?"` — Prevents Claude from charging ahead with wrong assumptions. Cheap insurance.

2. **Be specific about what you want.** "Add a login page" is vague. "Add a login page with email/password fields, a submit button, and error display. Use the existing auth API at /api/auth/login" gives Claude what it needs.

3. **Read what Claude writes.** Claude is good, not infallible. Review code before accepting it, especially for security, edge cases, and architectural decisions.

---

## Stage 2: First Week

### Context Is Finite — Manage It

Claude's context window is large but not infinite. As your conversation gets longer, Claude's quality degrades. Think of it like a desk — the more papers you pile on, the harder it is to find anything.

**Rules of thumb:**
- After ~20 back-and-forth exchanges, consider starting fresh or compacting
- One task per session. Don't ask Claude to build auth, then pivot to styling, then debug a database issue — all in one session
- If Claude starts repeating itself, forgetting earlier decisions, or producing lower-quality output, the context is full

**Compacting (clearing the desk without losing notes):**

Before compacting, save what matters:

```
Before we compact, save the critical context: what's our current objective,
what decisions have we made, what's the current state of the code, and
what should we do next. Write this to a compact-notes.md file, then compact.
```

Then use `/compact` to compress the conversation. Claude will re-read its notes on the next turn.

### Prompts Worth Memorizing

**Start of session:**
```
Read CLAUDE.md. Give me a brief status update — where we left off,
what we did last session, and what to tackle next.
```

**When debugging gets stuck:**
```
Forget everything you tried. Go through the problem again without
assuming anything.
```

**After writing a chunk of code:**
```
Now carefully read over all the new code you just wrote with fresh eyes,
looking for any obvious bugs, errors, or issues. Fix anything you find.
```

**Before compacting:**
```
Before we compact, save the critical context: what's our current objective,
what decisions have we made, what's the current state of the code, and
what should we do next. Write this to a compact-notes.md file, then compact.
```

### Parallel Sessions

You can run multiple Claude sessions at once — each gets clean context.

**How:** Open two terminals in VS Code, run `claude` in each. Give each a separate task.

**Good combos:**
- Session A writes a feature, Session B writes tests for the previous feature
- Session A builds code, Session B reviews what Session A committed (the "Writer/Reviewer" pattern — a fresh session reviewing code isn't biased by having just written it)

This doubles throughput on independent tasks.

### What Not To Do

- **Don't use Claude for formatting.** Use ESLint, Prettier, Black, or your language's formatter. Save Claude's context for actual reasoning.
- **Don't let Claude guess.** If it's making assumptions you haven't confirmed, stop it. Clarify. It's cheaper to pause than to undo.
- **Don't fight the context wall.** If quality drops, start a new session. Don't keep prompting hoping it gets better.

---

## Stage 3: First Month

Once you're comfortable with Claude Code basics, add structure.

### The Foundation Package

Every project you create should start with a standard set of files. This isn't ceremony — it's the minimum structure that makes Claude effective across sessions and makes your projects scannable months later.

**The structure:**
```
project-name/
├── CLAUDE.md              ← Claude's memory (auto-loaded)
├── AGENTS.md              ← Same content, for Codex/Cursor compatibility
├── README.md              ← Standard readme
├── project-meta.json      ← Metadata tracker (status, hours, lessons)
├── .claude/
│   ├── settings.json      ← Permissions & config
│   ├── commands/          ← Custom slash commands
│   └── agents/            ← Custom subagents
├── docs/
│   ├── changelog.md       ← Running log of changes per session
│   ├── decisions.md       ← Architecture decision records
│   └── FORDAN.md          ← Learning doc (what was built and why)
├── .env.example           ← Template for secrets (never commit .env)
└── .gitignore
```

### The Init Script

Don't create this by hand. There's a script:

```bash
~/scripts/init-project.sh my-project "A tool that does X" "node,react,typescript"
```

This creates the full structure, initializes git, and optionally creates a GitHub repo. One command, 30 seconds, every project starts right.

> **Setup:** Copy the init script to `~/scripts/init-project.sh` from the foundation guide repo and make it executable: `chmod +x ~/scripts/init-project.sh`

### project-meta.json

This file tracks your project's vital signs:

```json
{
  "name": "my-project",
  "status": "in-development",
  "created_date": "2026-02-27",
  "last_updated": "2026-02-27",
  "hours": {
    "manual_estimate": 0,
    "ai_assisted_actual": 0,
    "sessions_count": 0
  },
  "skills_used": [],
  "tools_used": ["claude-code"],
  "lessons_learned": [],
  "tech_stack": ["node", "react", "typescript"],
  "github_url": "",
  "live_url": ""
}
```

You don't update this manually. The `/wrap-up` command does it.

### /wrap-up — The Most Valuable Habit

At the end of every session, run:
```
/wrap-up
```

This single command updates five files:
1. **docs/changelog.md** — What changed this session
2. **project-meta.json** — Session count, hours, status, lessons learned
3. **docs/decisions.md** — Any architecture decisions made
4. **docs/FORDAN.md** — What was built, how it works, what you learned
5. **CLAUDE.md** — Current status section

Without `/wrap-up`, projects become black boxes within a week. With it, you (or Claude) can pick up any project cold and know exactly where things stand.

> The `/wrap-up` command lives in `.claude/commands/wrap-up.md`. The init script creates it. To make it available in ALL projects (recommended), copy it to `~/.claude/commands/wrap-up.md`.

### /status — Quick Orientation

Starting a session and forgot where you left off?
```
/status
```

Reads your `project-meta.json` and `CLAUDE.md`, gives you a quick summary: status, last updated, session count, hours saved, what's next.

### Retrofitting Existing Projects

Already have projects without the Foundation Package? Retrofit them:

```
Read through the codebase and create the missing foundation files:
1. CLAUDE.md based on what you see in the code
2. project-meta.json backfilled from git history
3. .claude/commands/wrap-up.md and status.md
4. docs/decisions.md backfilled from commits
5. docs/changelog.md backfilled from git log
6. docs/FORDAN.md learning narrative from the codebase
```

This works surprisingly well. Claude reads your code and git history and produces accurate foundation files in one session.

---

## Stage 4: Going Further

### CLAUDE.md Hierarchy

CLAUDE.md files stack. More specific overrides more general:

```
~/.claude/CLAUDE.md                    ← Global (all projects)
~/projects/CLAUDE.md                   ← Workspace level
~/projects/my-app/CLAUDE.md            ← Project level (most common)
~/projects/my-app/src/CLAUDE.md        ← Subfolder level
~/projects/my-app/CLAUDE.local.md      ← Personal overrides (gitignored)
```

Put your personal preferences (editor settings, commit style, communication tone) in the global file. Put project-specific context in the project file.

### Skills, Hooks, MCP, Subagents — When to Use What

| Mechanism | What It Does | When to Use It |
|-----------|-------------|----------------|
| **Skills** | Reusable knowledge/workflows | Cross-project patterns (frontend design, code review) |
| **Hooks** | Auto-run scripts at lifecycle points | Format on save, log on session end |
| **MCP** | Connect to external services | Notion sync, database access, APIs |
| **Subagents** | Delegate tasks to separate context | Code review, security audit, research |
| **Slash commands** | Stored prompts triggered manually | `/wrap-up`, `/status` |

**Start with:** Slash commands (`/wrap-up`, `/status`). Add MCP when you want external integrations. Add skills when you find yourself repeating the same instructions across projects. Hooks and subagents are advanced — wait until you have a specific need.

### The Project Scanner

Once you have 3+ projects with Foundation Packages, scan them:

```bash
~/scripts/scan-projects.sh
```

Outputs a health table: project name, status, last updated, hours saved, session count. Flags stale projects (not updated in 30+ days). Shows your most-used skills across all projects.

> **Setup:** Copy `scan-projects.sh` to `~/scripts/` from the foundation guide repo.

### Notion Integration (Optional)

If you want a visual dashboard of all your projects, set up a Notion hub:

1. Connect the Notion MCP server to Claude Code
2. Run `/setup-notion-hub` to create databases (Project Lab, Resources, Skills Catalog)
3. `/wrap-up` will automatically sync project status to Notion after each session
4. `/push-to-notion` for manual syncs

This is optional. The local files work fine on their own. Notion adds visibility if you're tracking a portfolio of projects.

See [website-notion-guide.md](website-notion-guide.md) for full setup details.

### The Full Guide

This getting-started doc covers the essentials. For deep dives into any topic — model effort levels, corporate deployment gotchas, community best practices, detailed prompts — read the full guide:

**[cc-foundation-guide.md](cc-foundation-guide.md)**

---

## Quick Reference

| When | Do This |
|------|---------|
| Starting a new project | `~/scripts/init-project.sh name "desc" "tech,stack"` |
| First session in a project | Have Claude interview you and write CLAUDE.md |
| Planning anything non-trivial | `Shift+Tab` for Plan Mode (Opus) |
| End of every session | `/wrap-up` |
| Start of every session | `/status` or ask Claude for a status update |
| Context getting heavy | Save notes, then `/compact` |
| Quality dropping | Start a fresh session |
| Debugging stuck | "Forget everything you tried. Start over without assumptions." |
| Portfolio health check | `~/scripts/scan-projects.sh` |

---

*This guide is part of the [Claude Code Foundation Guide](cc-foundation-guide.md) project. Found something missing or wrong? Update it — the guide eats its own dog food.*
