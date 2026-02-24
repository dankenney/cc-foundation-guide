# Claude Code Foundation Guide

### A Practical Operating System for Building with AI Coding Agents

> **Last updated:** 2026-02-24
> **Version:** 2.1  
> **Purpose:** A comprehensive, shareable reference for starting projects, managing context, choosing models, and maintaining quality when building with Claude Code (CC), OpenAI Codex, and related AI coding agents.  
> **Audience:** Anyone using AI coding agents — from first project to advanced workflows.  
> **This guide is itself a project.** Give it the foundation package at `~/projects/cc-foundation-guide/` and keep it alive.

---

## Table of Contents

0. [Quick-Start Decision Guide](#0-quick-start-decision-guide)
1. [Environment Strategy: Where & How to Build](#1-environment-strategy)
2. [Model Strategy: What to Use When](#2-model-strategy)
3. [The Foundation Package: What Every Project Needs](#3-the-foundation-package)
4. [CLAUDE.md vs AGENTS.md — When & Why](#4-claudemd-vs-agentsmd)
5. [Skills & Plugins Strategy](#5-skills--plugins-strategy)
6. [The Init Script: Automating Project Kickoff](#6-the-init-script) *(includes: Retrofitting Existing Projects, Global vs Project-Level Commands)*
7. [Project Tracker: Documenting What You Build](#7-project-tracker)
8. [CC Prompts: Copy-Paste Starters](#8-cc-prompts)
9. [Key Learnings from Research & Practice](#9-key-learnings)
10. [Quick Reference Card](#10-quick-reference-card)

---

## 0. Quick-Start Decision Guide

This section maps common scenarios to specific guidance in the document. It's organized by the two most common workflows: **building software** and **doing research/analysis**. Use it as a routing table — find your situation, follow the link.

Items marked with **\*** include notes from personal experience building AI-powered tools for sustainability consulting.

### The Software Development Lifecycle

Building an app, tool, script, automation, or any software artifact with AI coding agents.

#### Phase 1: Ideation & Setup

| Do This                                    | Because                                                                                                                                                                                                                                   | Details                                                                                    |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Choose your interface first                | CC Desktop, VS Code + CLI, and standalone CLI each have different strengths. Picking wrong means friction later.                                                                                                                          | [Section 1: Interface Options](#interface-options-cc-desktop-vs-vs-code-vs-standalone-cli) |
| Run the init script for every new project  | Even "quick" projects benefit from structure. A project without CLAUDE.md loses context between sessions.                                                                                                     | [Section 6: The Init Script](#6-the-init-script)                                           |
| Write CLAUDE.md before writing any code    | CC reads this at session start. A good CLAUDE.md reduces hallucinations and keeps CC aligned across sessions. **\*** *In practice, the first 30 minutes of a project should be CC interviewing you, then writing the CLAUDE.md together.* | [Section 3: CLAUDE.md contents](#what-each-file-does)                                      |
| Consider AGENTS.md if using multiple tools | If you use both CC and Codex (or Cursor/Zed), maintaining both files keeps context consistent across tools. If you only use CC, skip it.                                                                                                  | [Section 4: CLAUDE.md vs AGENTS.md](#4-claudemd-vs-agentsmd)                               |

#### Phase 2: Planning & Architecture

| Do This                                                     | Because                                                                                                                                                                                     | Details                                                |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Use Opus for planning, not Sonnet                           | Opus is the stronger reasoning model. Using Sonnet for architectural decisions leads to shallow plans that need rework.                                                                     | [Section 2: When to Use Opus](#when-to-use-each-model) |
| Enter Plan Mode (`Shift+Tab`)                               | Triggers Opus for planning while keeping Sonnet for execution. One toggle, automatic model switching.                                                                                       | [Section 2: Opus Plan Mode](#opus-plan-mode)           |
| End planning prompts with "any questions before you start?" | Prevents CC from charging ahead with wrong assumptions. Cheap insurance against wasted context. **\*** *This single habit improved output quality more than any other prompting technique.* | [Section 8: Prompts](#8-cc-prompts)                    |
| Log architecture decisions in `docs/decisions.md`           | Without a decision log, you'll forget why you chose a framework, database, or API approach — and CC will too after compaction.                                                              | [Section 3: decisions.md](#what-each-file-does)        |

#### Phase 3: Implementation

| Do This                                      | Because                                                                                                                 | Details                                                                         |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Use Sonnet (default) for execution           | Once the plan is clear, Sonnet is faster and cheaper for writing code. Opus is overkill for implementation.             | [Section 2: When to Use Sonnet](#when-to-use-each-model)                        |
| Use parallel sessions for independent tasks  | One session writing code + one session writing tests = 2x throughput. Each session gets clean context.                  | [Section 1: Parallel Sessions](#parallel-sessions--subagents)                   |
| Use the Writer/Reviewer pattern              | A fresh CC session reviewing code isn't biased by having just written it. Fresh context = honest review.                | [Section 1: Parallel Sessions](#parallel-sessions--subagents)                   |
| Write a reproducing test before fixing a bug | Forces CC to understand the actual problem before proposing a fix. Prevents "fix one thing, break another" loops.       | [Section 9: X Bookmarks learnings](#from-your-x-bookmarks-actionable-additions) |
| Monitor context usage                        | CC degrades as context fills up. If you're 70%+ full, compact or start a fresh session. Don't let it silently degrade.  | [Section 8: Context Check prompt](#context-management)                          |
| Compact on your terms                        | Before `/compact`, save critical context to a file so CC can reload it. Unmanaged compaction loses important decisions. | [Section 8: Before Compaction prompt](#context-management)                      |

#### Phase 4: Testing & Review

| Do This                                | Because                                                                                                   | Details                                                                       |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Have CC do a "fresh eyes" review       | After writing code, ask CC to re-read it critically. It catches bugs it introduced.                       | [Section 8: Fresh Eyes prompt](#code-quality)                                 |
| Use recursive self-improvement         | Have CC critique its own output → improve → critique again. Two rounds catches most issues.               | [Section 9: Community practices](#from-your-x-bookmarks-actionable-additions) |
| Never send an LLM to do a linter's job | Use ESLint, Prettier, Black, etc. for formatting and style. Save CC's context for actual reasoning tasks. | [Section 9: Community practices](#from-community-best-practices)              |

#### Phase 5: Deployment & Wrap-Up

| Do This                                    | Because                                                                                                                                                                                                                            | Details                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Run `/wrap-up` at the end of every session | Updates changelog, project tracker, decisions log, and learning doc in one command. Takes 30 seconds, saves hours of lost context. **\*** *The single most valuable habit. Without it, projects become black boxes within a week.* | [Section 8: End of Session](#session-management)                             |
| Update FORDAN.md after major milestones    | Plain-language explanation of what was built. Future You (or a colleague) can pick up the project cold.                                                                                                                            | [Section 3: FORDAN.md](#what-each-file-does)                                 |
| Hook doc updates to deployment             | Prevents documentation rot. If docs aren't updated at deploy time, they drift permanently.                                                                                                                                         | [Section 9: Doc rot prevention](#from-your-x-bookmarks-actionable-additions) |

---

### The Research & Analysis Lifecycle

Using AI coding agents for research, data analysis, report generation, or knowledge synthesis — not just software.

#### Phase 1: Framing the Question

| Do This                                      | Because                                                                                                                                                                                                                                                                                                                                                | Details                                                                                    |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| Use CC Desktop for research-heavy work       | CC Desktop's chat interface is better for iterative research than a terminal. Use it for analysis, synthesis, and document work. If you need to run scripts or build something, switch to VS Code.                                                                                                                                                     | [Section 1: Interface Options](#interface-options-cc-desktop-vs-vs-code-vs-standalone-cli) |
| Use Opus (max effort) for complex analysis   | Research and synthesis are reasoning-heavy tasks. Opus with high effort level produces dramatically better analytical output than Sonnet.                                                                                                                                                                                                              | [Section 2: When to Use Opus](#when-to-use-each-model)                                     |
| Still create a project folder with CLAUDE.md | Even for research projects, CC benefits from persistent context. A CLAUDE.md with "this is a research project about X, the key questions are Y, what we know so far is Z" keeps CC on track across sessions. **\*** *Used this for a Nike demand forecasting analysis — the CLAUDE.md became a living research brief that improved with each session.* | [Section 3: CLAUDE.md contents](#what-each-file-does)                                      |

#### Phase 2: Research & Data Gathering

| Do This                                     | Because                                                                                                                                   | Details                                                                              |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Use web search + MCP for data collection    | CC can search the web and pull from connected tools (Notion, Google Drive, etc.). Configure MCPs before starting a research project.      | [Section 5: MCP connections](#skills-vs-hooks-vs-mcp-vs-subagents--when-to-use-what) |
| Use subagents for parallel research threads | Delegate specific research questions to background agents while you focus on the main thread. Each gets clean context for their subtopic. | [Section 1: Parallel Sessions](#parallel-sessions--subagents)                        |
| Save intermediate findings to files         | Don't rely on context alone. Write research notes to `docs/` as you go. CC can always re-read files; it can't recover compacted context.  | [Section 8: Before Compaction](#context-management)                                  |

#### Phase 3: Analysis & Synthesis

| Do This                                 | Because                                                                                                                                                                                                                                              | Details                                                            |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Consider Opus 4.6 for synthesis tasks   | When combining multiple data sources into a coherent analysis, Opus 4.6 with high effort produces the most nuanced output.                                                                                                                           | [Section 2: Model Comparison](#when-to-use-each-model)             |
| Use deterministic code for calculations | If your analysis involves numbers, have CC write a script that performs the calculation. Don't let it do math in-context — it will make errors.                                                                                                      | [Section 9: Community practices](#from-community-best-practices)   |
| Use the Document Skills for output      | When the deliverable is a Word doc, PowerPoint, or PDF, use Anthropic's document skills rather than building from scratch. **\*** *PCAF agent specification was generated as a polished Word doc using document skills — saved hours of formatting.* | [Section 5: Skills Worth Installing](#skills-worth-installing-now) |

#### Phase 4: Deliverable & Documentation

| Do This                                   | Because                                                                                                                                  | Details                                             |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Run `/wrap-up` even for research sessions | Logs what questions were explored, what was found, and what's still open. Prevents "what did I learn last time?" syndrome.               | [Section 8: End of Session](#session-management)    |
| Use FORDAN.md for research conclusions    | Write up findings in plain language. Especially valuable when the research will inform a client deliverable or team discussion.          | [Section 3: FORDAN.md](#what-each-file-does)        |
| Archive research context before closing   | If you might return to this research later, save a comprehensive summary to a file. Named sessions help too, but files are more durable. | [Section 8: Before Compaction](#context-management) |

---

### Model Quick-Reference (When to Use What)

| Scenario                                | Recommended Model      | Effort Level | Interface     |
| --------------------------------------- | ---------------------- | ------------ | ------------- |
| Planning architecture for a new app     | Opus (Plan Mode)       | High         | VS Code + CLI |
| Implementing features from a clear plan | Sonnet (default)       | Medium       | VS Code + CLI |
| Complex research synthesis              | Opus 4.6               | Max          | CC Desktop    |
| Writing a report or analysis            | Opus                   | High         | CC Desktop    |
| Quick file edits, simple scripts        | Sonnet                 | Low          | Either        |
| Code review                             | Sonnet (fresh session) | Medium       | VS Code + CLI |
| Debugging a stubborn issue              | Opus                   | High         | VS Code + CLI |
| Data processing automation              | Sonnet                 | Medium       | VS Code + CLI |
| Brainstorming / ideation                | Opus                   | Medium       | CC Desktop    |
| Formatting, cleanup, refactoring        | Sonnet                 | Low          | VS Code + CLI |

---

## 1. Environment Strategy

### Verdict: Local-First with GitHub Sync

The best setup for AI coding agents is local development with GitHub as your versioning/deployment layer. Here's why:

- **CC reads your full file system.** CLAUDE.md files, folder hierarchy, auto memory — all of this depends on local files. Cloud environments lose the hierarchical CLAUDE.md loading that makes CC powerful.
- **Context is king.** CC's context window fills fast. Local means CC can read your files without network latency, and your CLAUDE.md files load automatically based on directory hierarchy.
- **GitHub is your deployment/versioning layer, not your dev environment.** Push when ready, deploy from there. Don't develop there.
- **Codex (OpenAI) is the exception.** Codex runs in a cloud sandbox by design. Use AGENTS.md there (see [Section 4](#4-claudemd-vs-agentsmd)). Use Codex for tasks where you want a sandboxed environment or when leveraging OpenAI-specific models.

### Interface Options: CC Desktop vs VS Code vs Standalone CLI

Claude Code is available in three forms. Each is best for different work:

#### CC Desktop (Claude Desktop App → Cowork Tab)

**Best for:** Research, analysis, document work, iterative chat, and projects where you're directing CC more than writing code yourself.

- Visual chat interface with rich formatting
- File/folder access via Cowork panel (point it at a project folder)
- Better for long, conversational sessions — reading research, synthesizing findings, iterating on analysis
- CLAUDE.md is still loaded if you point Cowork at a project folder
- **Limitation:** No parallel sessions, no background agents, no terminal access

**Use when:** You're doing research, writing reports, analyzing data, or working on a project where the primary output isn't code. Also good for learning CC — the visual interface is less intimidating than the terminal.

**\*** *CC Desktop is where most research and analysis projects happen in practice. The terminal is for building; Desktop is for thinking.*

#### VS Code + CC CLI (Terminal Panel)

**Best for:** Software development, multi-file projects, anything involving code + tests + deployment.

- Full terminal access with CC's status line showing context usage
- Multiple terminal panels = parallel CC sessions on the same project
- See your code in the editor while CC works in the terminal
- Background agents and subagent support
- All slash commands, hooks, and MCP integrations available
- Git integration visible in VS Code's source control panel

**Use when:** You're building software, debugging, or working on any project with multiple files that need coordinated changes.

#### Standalone CLI (Terminal Only)

**Best for:** Quick tasks, scripting, headless server work, or when you prefer a minimal environment.

- Identical capabilities to VS Code CLI — just no IDE alongside it
- Slightly lower resource usage
- Works over SSH for remote development

**Use when:** You're comfortable in a terminal and don't need the visual file browser. Some people prefer this for focus.

### Your Setup Checklist

| Component         | Recommendation                                        | Notes                                               |
| ----------------- | ----------------------------------------------------- | --------------------------------------------------- |
| IDE               | VS Code (free, extensible)                            | For code projects; CC Desktop for research          |
| CC Interfaces     | Install both CLI and Desktop                          | Use CLI for building, Desktop for research/analysis |
| Working Directory | `~/projects/` or cloud-synced folder                  | Use OneDrive/Dropbox for backup                     |
| Git               | Initialize every project from the start               | Even for non-code projects                          |
| Model             | **Opus Plan Mode** (auto-switches Opus↔Sonnet)        | See [Section 2](#2-model-strategy)                  |
| Permissions       | Pre-allow safe bash commands, file reads, web fetches | Reduces permission prompts                          |

### Parallel Sessions & Subagents

If you're running one CC session at a time doing things sequentially, you're underusing the tool. Here's what to layer in:

**Parallel terminal sessions** — Open multiple terminals in VS Code, each running `claude` in the same project folder. Use these for truly independent tasks (e.g., one session builds a feature while another writes documentation). Each gets its own clean context.

**Background agents** — Within a single session, delegate related side tasks. CC spawns a separate agent that works independently and notifies you when done. Best for tasks that need context from your current conversation but don't need to block you.

```
"Compile all the decisions we've made into docs/decisions.md — do this in the background"
```

**Custom subagents** (`.claude/agents/`) — Pre-configured specialists. A reviewer agent that only has read access. A test-runner that validates changes. These run in isolated context, preventing your main session from getting cluttered.

**Writer/Reviewer pattern** — Have one session write code, then a fresh session review it. The reviewer isn't biased toward code it just wrote (fresh context = honest review).

---

## 2. Model Strategy

### Opus Plan Mode

Rather than manually switching between Opus and Sonnet, CC has a built-in feature called **Opus Plan Mode**. Set it once in `/model` and forget about it:

- **Plan mode** (`Shift+Tab` to enter): CC uses Opus with thinking — the heavy reasoning model. This is where it interviews you, designs architecture, maps out implementation steps.
- **Execution mode** (normal): CC drops to Sonnet — faster, cheaper, excellent at implementing plans that are already well-defined.

The workflow becomes: enter plan mode → CC thinks through the approach with Opus → you approve → CC builds with Sonnet.

### When to Use Each Model

| Model          | Strengths                                                    | Best For                                                                                                | Cost/Speed                                  |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| **Opus 4.6**   | Deepest reasoning, best at complex analysis, nuanced writing | Architecture planning, research synthesis, complex debugging, first-time analysis of unfamiliar domains | Slowest, highest cost (within subscription) |
| **Sonnet 4.5** | Strong execution, good code generation, fast                 | Implementing clear plans, code writing, refactoring, standard debugging, tests                          | Fast, moderate cost                         |
| **Haiku 4.5**  | Quick responses, simple tasks                                | Formatting, simple lookups, trivial edits, quick Q&A                                                    | Fastest, lowest cost                        |

### Effort Levels

For Opus 4.6, you can adjust **effort level** using the arrow keys in the `/model` UI:

- **Max effort:** Complex architectural decisions, novel problem domains, research synthesis across many sources
- **High effort:** Standard planning, analysis, writing deliverables
- **Medium effort:** Implementation with some ambiguity, moderate debugging
- **Low effort:** Simple edits, formatting, straightforward tasks

The effort level affects how much "thinking" Opus does before responding. Higher effort = more internal reasoning = better output for hard problems = slower response.

### Switching Models Mid-Session

You can switch models at any time using the `/model` command. Practical patterns:

- **Start with Opus for planning**, switch to Sonnet for building (or use Plan Mode to automate this)
- **Hit a wall debugging?** Switch to Opus — its deeper reasoning often spots what Sonnet misses
- **Doing rote work?** Drop to Haiku for speed
- **Reviewing CC's own output?** Switch to Opus in a fresh session for unbiased review

### CC Desktop Model Selection

In CC Desktop, model selection works differently:

- Use the model picker in the interface (usually top of the conversation)
- Opus is recommended for research and analysis sessions (Desktop's primary use case)
- Sonnet works well for simple document editing and quick tasks in Desktop

---

## 3. The Foundation Package

Every new project should start with this structure. The init script ([Section 6](#6-the-init-script)) automates creation.

```
project-name/
├── CLAUDE.md                  # Project context for CC (auto-loaded)
├── AGENTS.md                  # Mirror of CLAUDE.md for Codex/Cursor (if needed)
├── README.md                  # Standard project readme
├── project-meta.json          # Tracker data (feeds your portfolio)
├── .claude/
│   ├── settings.json          # CC settings (permissions, env vars)
│   ├── agents/                # Custom subagents
│   │   └── reviewer.md        # Example: code review agent
│   ├── commands/              # Custom slash commands
│   │   ├── wrap-up.md         # End-of-session documentation
│   │   └── status.md          # Quick project status update
│   └── skills/                # Project-specific skills (optional)
├── docs/
│   ├── decisions.md           # Architecture decision log
│   ├── changelog.md           # Running changelog
│   └── FORDAN.md              # "Claude Teacher" learning doc
├── .env.example               # Environment variable template
└── .gitignore
```

### What Each File Does

**CLAUDE.md** — The most important file. CC reads this automatically at session start. It should contain:

1. **Project purpose** (1-2 sentences — what is this and why does it exist)
2. **Tech stack** (languages, frameworks, key dependencies)
3. **Architecture overview** (how the pieces connect, high-level only)
4. **Key commands** (build, test, deploy — exact commands CC should use)
5. **Conventions** (naming patterns, file organization rules)
6. **Status** (current phase, what's in progress, what's blocked)
7. **Gotchas** (things that will trip CC up if it doesn't know about them)

Keep it lean. Per HumanLayer's analysis, CC's system prompt already uses ~50 instructions of the ~150-200 an LLM can reliably follow. Your CLAUDE.md should be focused, not exhaustive.

**project-meta.json** — The data layer that feeds your tracker and portfolio:

```json
{
  "name": "project-name",
  "description": "Brief description of the project",
  "status": "ideating",
  "created_date": "2026-02-15",
  "last_updated": "2026-02-15",
  "hours": {
    "manual_estimate": 0,
    "ai_assisted_actual": 0,
    "sessions_count": 0
  },
  "skills_used": [],
  "tools_used": [],
  "plugins_used": [],
  "lessons_learned": [],
  "tech_stack": [],
  "github_url": "",
  "live_url": "",
  "tags": []
}
```

**docs/FORDAN.md** — Inspired by the "Claude Teacher" pattern (via @zarazhangrui). At the end of each project or major milestone, CC writes a plain-language explanation of:

- What was built and why
- Technical architecture in simple terms
- How the pieces connect
- Bugs encountered and how they were fixed
- New technologies used and why they were chosen
- Lessons that transfer to future projects

This is written in an engaging, analytical voice — with enough context that anyone can pick it up cold. **\*** *This turned out to be the most personally valuable file. Re-reading these months later reconstructs the entire project faster than reading the code.*

**docs/decisions.md** — Architecture Decision Records. When CC (or you) makes a significant choice (framework, database, API approach), it gets logged here with the reasoning. This prevents the "why did we do it this way?" problem.

**docs/changelog.md** — Running log of what changed per session. CC appends to this using the `/wrap-up` command.

---

## 4. CLAUDE.md vs AGENTS.md

### CLAUDE.md

- **Used by:** Claude Code (CLI and Desktop)
- **Auto-loaded:** Yes, hierarchically (parent directories down to current)
- **Location:** Project root, or `.claude/CLAUDE.md`, or `~/.claude/CLAUDE.md` for global
- **Case sensitive:** Yes — must be uppercase `CLAUDE.md`
- **Best for:** All CC projects, local development

### AGENTS.md

- **Used by:** OpenAI Codex, Cursor, Zed, OpenCode, and other AI coding tools
- **Auto-loaded:** Yes, by the respective tool
- **Location:** Project root
- **Best for:** Projects where you use Codex or want cross-tool compatibility

### Recommendation

If you use both CC and Codex, maintain both files. They should contain the same core content (project context, conventions, commands), but you can add tool-specific instructions:

- **CLAUDE.md** can reference CC-specific features: subagents, `/compact`, session management, MCP tools
- **AGENTS.md** can reference Codex-specific features: sandbox behavior, model preferences

A practical approach: maintain CLAUDE.md as your source of truth, and have CC generate AGENTS.md from it:

```
Read CLAUDE.md and create an AGENTS.md that contains the same project context 
but formatted for OpenAI Codex. Remove any CC-specific references (subagents, 
/compact, MCP) and add a note that this project also uses Claude Code.
```

If you only use Claude Code, skip AGENTS.md entirely — it adds maintenance burden without value.

---

## 5. Skills & Plugins Strategy

### Should You Tell CC to Search for Skills?

Short answer: **No, don't add a blanket instruction to search for skills.** CC doesn't reliably auto-invoke skills without being explicitly told to. Adding "always search for skills" to your CLAUDE.md wastes context and can lead CC down rabbit holes.

### Better Approach: Curated Skills Library

Maintain a **global skills reference** in your `~/.claude/CLAUDE.md` (user-level) that lists your installed skills and when to use them. CC will see this in every session and can reference skills when relevant.

```markdown
## Installed Skills

### Document Skills (Anthropic)
- **PDF, DOCX, PPTX, XLSX** — Use when creating/editing Office documents
- Install: `/plugin install document-skills@anthropic-agent-skills`

### Frontend Design (Vercel/Anthropic)  
- Use when building web UIs that need polished design
- Install: `npx skills add anthropics/skills --skill frontend-design`

### Humanizer (blader)
- Use when writing content that needs to avoid AI writing tells
- Repo: github.com/blader/humanizer
```

### Skills vs Hooks vs MCP vs Subagents — When to Use What

| Mechanism          | Use When                                       | Example                                  |
| ------------------ | ---------------------------------------------- | ---------------------------------------- |
| **Skills**         | Reusable knowledge/workflows across projects   | Frontend design best practices           |
| **Hooks**          | Automated actions at specific lifecycle points | Auto-format on save, log on session end  |
| **MCP**            | Connecting to external services                | Notion sync, Supabase, GitHub            |
| **Subagents**      | Delegating focused tasks in a separate context | Code review, security audit              |
| **Slash commands** | Stored prompts you trigger manually            | `/wrap-up`, `/status`, `/push-to-notion` |
| **Plugins**        | Bundled packages of skills/commands/agents     | `everything-claude-code`                 |

### Skills Worth Installing Now

1. **Document Skills** (Anthropic) — For creating decks, docs, and spreadsheets
2. **Frontend Design** (Vercel/Anthropic) — For polished web UIs
3. **Humanizer** (blader) — For content that doesn't read as AI-generated
4. **Supabase Agent Skills** — If using Supabase as a backend

### Your Running Skills/Plugins Tracker

Add this to your global `~/.claude/CLAUDE.md`:

```markdown
## Skills & Plugins Log
When I install a new skill or plugin, append it here with date and purpose.
When I learn a significant lesson about using a skill, note it here.

| Date | Skill/Plugin | Purpose | Lessons |
|------|-------------|---------|---------|
| 2026-02-15 | document-skills | Office doc creation | — |
```

---

## 6. The Init Script

### CC Prompt to Create the Init Script

Copy and paste this into a CC session to generate your project scaffolding tool:

```
Create a bash script at ~/scripts/init-project.sh that automates new project setup.

The script should:

1. Accept arguments: project name (required), description (optional), 
   tech stack (optional, comma-separated)

2. Create the following folder structure under ~/projects/[project-name]/:
   - CLAUDE.md (populated from template below)
   - AGENTS.md (copy of CLAUDE.md, with note about Codex compatibility)
   - README.md (standard readme with project name and description)
   - project-meta.json (tracker metadata, see schema below)
   - .claude/settings.json (with ENABLE_TOOL_SEARCH: true for on-demand MCP)
   - .claude/agents/ (empty, with a README explaining how to add agents)
   - .claude/commands/wrap-up.md (end-of-session documentation command)
   - .claude/commands/status.md (quick status check command)
   - docs/decisions.md (empty template with headers)
   - docs/changelog.md (initialized with "Project created" entry)
   - docs/FORDAN.md (empty template with learning doc structure)
   - .env.example (empty template)
   - .gitignore (standard Node/Python ignores + .env + .claude/settings.local.json)

3. Initialize git and make initial commit

4. Optionally create GitHub repo via gh CLI (prompt user)

5. Print a summary of what was created

CLAUDE.md template should include these sections with placeholder text:
- Project Purpose
- Tech Stack  
- Architecture
- Key Commands (build, test, deploy)
- Conventions
- Current Status
- Gotchas
- Installed Skills (reference to global skills list)

project-meta.json schema:
{
  "name": "[project-name]",
  "description": "[description or empty]",
  "status": "ideating",
  "created_date": "[today ISO]",
  "last_updated": "[today ISO]",
  "hours": {
    "manual_estimate": 0,
    "ai_assisted_actual": 0,
    "sessions_count": 0
  },
  "skills_used": [],
  "tools_used": [],
  "plugins_used": [],
  "lessons_learned": [],
  "tech_stack": [parsed from argument or empty],
  "github_url": "",
  "live_url": "",
  "tags": []
}

The /wrap-up command (.claude/commands/wrap-up.md) should contain:
---
description: End-of-session documentation and tracker update
---
Review what we accomplished in this session. Update the following files:

1. docs/changelog.md — append a dated entry summarizing changes made. 
   Write in a direct, analytical voice.

2. project-meta.json — update:
   - last_updated to now
   - increment sessions_count
   - adjust status if project phase changed
   - add any new entries to skills_used, tools_used, plugins_used
   - update hours.ai_assisted_actual (estimate total hours spent with AI)
   - update hours.manual_estimate (estimate how long this would take without AI)
   - add a brief lesson_learned entry if applicable

3. docs/decisions.md — log any architecture decisions made this session

4. docs/FORDAN.md — if this was a significant session, update the learning 
   document with what was built, how it works, and what was learned. Write 
   engagingly, not like documentation.

5. CLAUDE.md — update the Current Status section to reflect where we are.

Write all entries in a direct, analytical tone — like a senior consultant
documenting outcomes for a stakeholder who needs to understand what happened
and why it matters.

Note: This is the base /wrap-up command. If you later set up a Notion hub
(see website-notion-guide.md), the /wrap-up command is upgraded to include
a Part 2 that syncs project status to Notion after updating local files.
The /setup-notion-hub command handles this upgrade automatically.

The /status command (.claude/commands/status.md) should contain:
---
description: Quick project status summary
---
Read project-meta.json and CLAUDE.md. Provide a brief status report:
- Project name and current status
- Last updated date and session count
- Hours saved estimate (manual vs AI-assisted)
- What's in progress and what's next
- Any blockers or decisions needed

Make the script executable and test it by creating a sample project.
```

### Retrofitting Existing Projects

Not every project starts with `init-project.sh`. If you have an existing project that predates the Foundation Package, you can retrofit it. **\*** *This happened with the ROI tool — built over 5 sessions before the foundation guide existed. Retrofitting took one session and immediately improved cross-tool context.*

**CC Prompt to Retrofit:**

```
This is an existing project that doesn't have Foundation Package scaffolding.
Read through the codebase (README, docs, configs) and create the missing
foundation files:

1. CLAUDE.md — Project brief based on what you see in the code
2. AGENTS.md — Mirror for Codex/Cursor compatibility
3. project-meta.json — Backfill metadata from git history
4. .claude/commands/wrap-up.md and status.md
5. docs/decisions.md — Backfill key decisions from commit history and code
6. docs/changelog.md — Backfill from git log
7. docs/FORDAN.md — Write learning narrative from what's in the codebase

For decisions.md and changelog.md, use git log and existing docs to
reconstruct history. Don't invent dates — use what's available.
```

**Key differences from a fresh init:**
- You're *inferring* context from existing code, not defining it from scratch
- Backfilling `project-meta.json` requires estimating hours and session counts
- Existing docs (README, runbooks) should be referenced in CLAUDE.md, not duplicated
- The `docs/` directory may already exist with other files — add foundation files alongside them

### Global vs Project-Level Commands

Commands in `.claude/commands/` are only available when CC is running inside that project directory. This is a common gotcha — you create `/wrap-up` in one project and wonder why it doesn't appear in another.

**To make a command available in ALL projects**, put it in:

```
~/.claude/commands/wrap-up.md    ← Available everywhere
```

**Project-level commands** stay in:

```
~/projects/my-app/.claude/commands/wrap-up.md    ← Only in this project
```

**Recommendation:** Keep `/wrap-up` and `/status` **global** (in `~/.claude/commands/`) since they work the same everywhere. Keep project-specific commands (like `/setup-notion-hub`) at the project level.

The init script creates commands at the project level by default. After you've verified they work, move the universally useful ones to `~/.claude/commands/`.

---

## 7. Project Tracker

### Architecture: Local Files with Optional Central Hub

Each project maintains its own `project-meta.json` that CC updates via `/wrap-up`. This is your source of truth — fast, always available, no external dependencies.

For a cross-project view, you have two options:

**Option A: Local scan script** — A bash script that scans all project folders and prints a summary table. Zero dependencies, works offline.

**Option B: Notion hub (or similar)** — Push updates to a Notion database via MCP for mobile access, shareability, and richer views. See the separate [Website & Notion Hub Guide](./website-notion-guide.md) for setup.

Both options work with the same underlying `project-meta.json` files. Pick one or use both.

### CC Prompt to Set Up the Local Scanner

```
I want to set up a project tracking system. For now, let's handle the local 
side.

1. Create a bash script at ~/scripts/scan-projects.sh that:
   - Scans ~/projects/*/project-meta.json
   - Prints a formatted summary table: project name, status, last updated, 
     hours saved, session count
   - Highlights projects not updated in 7+ days
   - Shows total hours saved across all projects
   - Shows most-used skills across all projects

2. Make it runnable as a quick health check: 
   "How are my projects doing?"

This is a read-only diagnostic script — it doesn't modify anything.
```

### For Codex (Cloud) Projects

If you're running projects on Codex/GitHub, add a GitHub Action that updates `project-meta.json` on push:

```
Create a GitHub Action at .github/workflows/update-tracker.yml that:
- Triggers on push to main
- Reads the git diff to understand what changed
- Updates project-meta.json with last_updated date
- Commits the update
```

### Semi-Automatic Updates via Hooks

Use CC's async hooks to auto-log session timestamps without blocking your work:

```json
// .claude/settings.json
{
  "env": {
    "ENABLE_TOOL_SEARCH": "true"
  },
  "hooks": {
    "SessionEnd": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"Session ended at $(date -u +%Y-%m-%dT%H:%M:%SZ)\" >> docs/changelog.md",
        "async": true
      }]
    }]
  }
}
```

### Keeping This Guide Updated

This foundation guide is itself a project. To keep it from going stale:

1. **Give it the foundation package.** It lives at `~/projects/cc-foundation-guide/` with its own CLAUDE.md and project-meta.json.

2. **Periodic review prompt** (run monthly or when it feels stale):
   
   ```
   Review this foundation guide against my recent project sessions and any 
   new discoveries. What's outdated? What's missing? What practices have I 
   adopted that aren't documented here? Suggest specific updates.
   ```

3. **CC can update it proactively.** Add this to the guide's CLAUDE.md:
   
   ```
   When a new best practice, tool, or workflow improvement is discovered 
   during any project session, suggest adding it to this guide if it's 
   broadly applicable across projects.
   ```

---

## 8. CC Prompts: Copy-Paste Starters

### Session Management

**Start of Session:**

```
Read CLAUDE.md and project-meta.json. Give me a brief status update on where 
we left off. What's the current status, what did we do last session, and 
what should we tackle next?
```

**End of Session:**

```
/wrap-up
```

### Project Kickoff

**When Starting a Brand New Project:**

```
Run ~/scripts/init-project.sh [project-name] "[description]" "[tech,stack]"
```

Then in the new project folder:

```
I just initialized this project. Let's start in plan mode. Interview me 
about what I'm building — ask questions to understand the goal, users, 
constraints, and technical requirements. Then update CLAUDE.md with a 
comprehensive project brief.
```

### Code Quality

**When Debugging Gets Stuck:**

```
Forget everything you did. Go through the plan again without assuming anything.
```

**Fresh Eyes Review:**

```
Now carefully read over all the new code you just wrote with fresh eyes, 
looking carefully for any obvious bugs, errors, or issues. Fix anything 
you uncover.
```

**After Major Feature Completion:**

```
Update docs/FORDAN.md with what we just built. Explain the technical 
architecture, how the pieces connect, the technologies used, why we made 
these decisions, and lessons learned including bugs we hit and how we fixed 
them. Make it engaging to read, not like a textbook. Use analogies where 
they help.
```

### Context Management

**Context Check:**

```
What do you currently have in context from our conversation? Give me a summary.
```

**Before Compaction:**

```
Before we compact, save the critical context: what's our current objective, 
what decisions have we made, what's the current state of the code, and 
what should we do next. Write this to a compact-notes.md file, then compact.
```

### Monthly Review

```
Run ~/scripts/scan-projects.sh and show me the output. Review for anything 
stale (not updated in 30+ days). For stale projects, suggest whether to 
mark them as paused or if there's an obvious next step. Also check if any 
lessons_learned entries would be useful to add to the foundation guide.
```

---

## 9. Key Learnings from Research & Practice

### From Boris Cherny (Creator of Claude Code)

- There's no single "right" setup — experiment and iterate
- Use **Opus Plan Mode** in `/model` to auto-switch Opus for planning, Sonnet for execution
- For Opus 4.6, adjust **effort level** with arrow keys — dial up for hard decisions, down for straightforward work
- Enable on-demand MCP loading (`ENABLE_TOOL_SEARCH: true`) to save context
- Async hooks are available for non-blocking background tasks
- Subagents and parallel sessions are the biggest underused capability — use the Writer/Reviewer pattern

### From Hannah Stulberg's Series

- CLAUDE.md files stack hierarchically: more specific folders override general ones
- Context is a "drawer" — monitor how full it is, compact on your terms
- Use separate sessions for separate tasks (clean context)
- Name and resume sessions to preserve context across time
- Always compare before syncing (Notion or any external tool)
- Review everything CC creates, especially custom commands

### From Community Best Practices

- Never send an LLM to do a linter's job — use deterministic tools for style (HumanLayer)
- CLAUDE.md should have as few instructions as possible, focused on universal context (HumanLayer)
- Don't @-mention doc files in CLAUDE.md (bloats context). Instead, tell CC when and why to read them (Shrivu Shankar)
- Use Opus for planning, Sonnet for execution (`/model` command) — separate strategic from operational
- Periodically trim CLAUDE.md: if content is about finished projects or outdated decisions, cut it

### From Deployment & Corporate Environments

Hard-won lessons from deploying AI-built apps to corporate networks. **\*** *Every one of these caused real production issues.*

- **Corporate firewalls silently block tunnels.** Cloudflare tunnels, ngrok, and similar tools may work on personal machines but fail silently on corporate networks. Use managed hosting (Vercel, Netlify) for anything testers on corporate machines need to reach.
- **Never throttle auth by IP on corporate networks.** Shared IP addresses mean one person's failed password locks out the entire office. Scope rate limiting to a browser-generated client ID cookie instead.
- **Nested subdomains need Advanced TLS Certificates.** `app.api.domain.com` won't work with standard wildcard certs (`*.domain.com`). Cloudflare needs an Advanced Certificate covering `*.subdomain.domain.com`. Set this up proactively.
- **Serverless `/tmp` is ephemeral.** On Vercel, Lambda, etc., any data written to `/tmp` vanishes on cold starts. Use proper storage (Vercel Blob, S3, database) for anything you need to persist.
- **Test on corporate machines early.** Don't wait for UAT to discover that your app doesn't work behind a corporate proxy. Get access to the target environment as early as possible.

### From Your X Bookmarks (Actionable Additions)

- **Write a test first:** When reporting a bug, have CC write a reproducing test before fixing (@nbaschez)
- **Claude Teacher pattern:** Have CC write FORDAN.md explaining each project in plain language (@zarazhangrui)  
- **Doc rot prevention:** Hook on deploy to check/update docs, changelog on all PRs, regular sweep (@clairevo)
- **Recursive self-improvement:** Have CC critique its own output, improve, then critique again (@VibeMarketer_)
- **Ask questions first:** End prompts with "Before you respond, are there any questions?" (@Object_Zero_)
- **Taskmaster pattern:** For long-running tasks, use taskmaster to prevent CC from stopping early (@blader)

---

## 10. Quick Reference Card

### File Hierarchy (What CC Loads)

```
~/.claude/CLAUDE.md            ← Global (all projects, personal preferences)
~/projects/CLAUDE.md           ← Workspace level (shared context)
~/projects/my-app/CLAUDE.md    ← Project level (loaded at session start)
~/projects/my-app/src/CLAUDE.md ← Subfolder (loaded when CC reads files here)
~/projects/my-app/CLAUDE.local.md ← Personal overrides (gitignored)
```

### Key CC Commands

| Command        | What It Does                                |
| -------------- | ------------------------------------------- |
| `/init`        | Bootstrap a CLAUDE.md for current project   |
| `/compact`     | Summarize context to free space             |
| `/memory`      | Open memory files for editing               |
| `/model`       | Switch models, set plan mode, adjust effort |
| `/agents`      | Manage custom subagents                     |
| `/tasks`       | View background agent status                |
| `/permissions` | Manage bash/read/write permissions          |
| `/plugin`      | Browse and install marketplace plugins      |
| `Shift+Tab`    | Toggle plan mode (Opus↔Sonnet)              |
| `/cost`        | Check token usage for current session       |

### Settings to Configure Immediately

```json
// .claude/settings.json (project level)
{
  "env": {
    "ENABLE_TOOL_SEARCH": "true"
  },
  "permissions": {
    "allow": [
      "Read(*)",
      "Bash(ls *)",
      "Bash(cat *)",
      "Bash(cd *)",
      "Bash(mv *)",
      "Bash(cp *)",
      "Bash(open *)",
      "Bash(git *)",
      "WebFetch(domain:*)"
    ]
  }
}
```

### The Pipeline

```
init-project.sh → Work with CC → /wrap-up → Local tracking updated
                                           → Optional: push to Notion/portfolio
                                           → Check anytime: ~/scripts/scan-projects.sh
```

---

## Appendix: Resources Referenced

| Resource                       | URL                                           | Why It Matters                       |
| ------------------------------ | --------------------------------------------- | ------------------------------------ |
| Hannah Stulberg's CC Series    | hannahstulberg.substack.com                   | Best non-technical CC workflow guide |
| Boris Cherny's Tips            | @bcherny on X                                 | Direct from CC's creator             |
| Anthropic Best Practices       | docs.anthropic.com/en/docs/claude-code/       | Official documentation               |
| Everything Claude Code         | github.com/affaan-m/everything-claude-code    | Battle-tested config collection      |
| HumanLayer CLAUDE.md Guide     | humanlayer.dev/blog/writing-a-good-claude-md  | Context engineering deep dive        |
| Karpathy-Derived Skills        | github.com/forrestchang/andrej-karpthy-skills | 70 lines of distilled coding wisdom  |
| Shrivu Shankar's Feature Guide | blog.sshh.io                                  | Advanced CC usage patterns           |

---

*This guide is a living document. Update it as your workflow evolves.*
