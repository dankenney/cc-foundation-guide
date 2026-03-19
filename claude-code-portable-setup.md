# Claude Code Portable Setup

> **Generated:** 2026-03-18
> **Purpose:** Recreate Dan's full Claude Code environment on a new machine.
> **Source machine:** Windows 11 + Git Bash

---

## Quick Setup Checklist

1. Install Claude Code CLI
2. Copy `~/.claude/CLAUDE.md` (global instructions)
3. Copy `~/.claude/rules/` directory (4 rule files)
4. Copy `~/.claude/settings.json` (hooks, permissions, MCP, plugins)
5. Copy `~/.claude/commands/` directory (9 command files)
6. Copy `~/.claude/skills/` directory (2 skill files + excalidraw-diagrams/)
7. Copy `~/.claude/agents/` directory (2 agent files)
8. Re-enable marketplace plugins (listed below)
9. Re-authorize MCP servers (Notion, Linear)

---

## 1. settings.json

> `~/.claude/settings.json`

```json
{
  "env": {
    "ANTHROPIC_MODEL": "claude-sonnet-4-5-20250929"
  },
  "autoUpdatesChannel": "latest",
  "hooks": [
    {
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.(js|jsx)$\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "which prettier >/dev/null 2>&1 && prettier --write \"$FILE_PATH\" || echo '[Hook] Prettier not installed. Run: npm install -g prettier'"
        }
      ],
      "description": "Auto-format JavaScript files with Prettier"
    },
    {
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.(ts|tsx)$\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "which prettier >/dev/null 2>&1 && prettier --write \"$FILE_PATH\" || echo '[Hook] Prettier not installed'"
        }
      ],
      "description": "Auto-format TypeScript files with Prettier"
    },
    {
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.py$\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "which black >/dev/null 2>&1 && black \"$FILE_PATH\" || echo '[Hook] Black not installed. Run: pip install black'"
        }
      ],
      "description": "Auto-format Python files with Black"
    },
    {
      "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\.ts$\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "if [ -f tsconfig.json ]; then which tsc >/dev/null 2>&1 && tsc --noEmit || echo '[Hook] TypeScript not installed'; fi"
        }
      ],
      "description": "Type check TypeScript files after editing"
    },
    {
      "matcher": "tool == \"Bash\" && tool_input.command matches \"^git commit\"",
      "hooks": [
        {
          "type": "PreToolUse",
          "command": "if [ -f package.json ] && grep -q '\"lint\"' package.json; then npm run lint || echo '[Hook] Linting failed - fix errors before committing'; fi"
        }
      ],
      "description": "Run linter before git commits (if npm lint script exists)"
    },
    {
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.env\"",
      "hooks": [
        {
          "type": "PreToolUse",
          "command": "echo '[Hook] WARNING: Creating .env file. Ensure it is in .gitignore!' >&2"
        }
      ],
      "description": "Warn when creating .env files"
    },
    {
      "matcher": "tool == \"Bash\" && tool_input.command matches \"gh pr create\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "echo '[Hook] PR created. Checking GitHub Actions status...' && sleep 2 && gh pr checks || echo '[Hook] Run: gh pr checks to see CI status'"
        }
      ],
      "description": "Check CI status after PR creation"
    },
    {
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"(test|spec)\\.(js|ts|py)$\"",
      "hooks": [
        {
          "type": "PostToolUse",
          "command": "echo '[Hook] Test file created/modified. Run tests to verify.'"
        }
      ],
      "description": "Reminder to run tests after modifying test files"
    }
  ],
  "mcpServers": {
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    }
  },
  "enabledPlugins": {
    "claude-md-management@claude-plugins-official": true,
    "frontend-design@claude-plugins-official": true,
    "linear@claude-plugins-official": true,
    "plugin-dev@claude-plugins-official": true,
    "ralph-loop@claude-plugins-official": true,
    "security-guidance@claude-plugins-official": true,
    "supabase@claude-plugins-official": true,
    "superpowers@claude-plugins-official": true,
    "code-review@claude-plugins-official": true,
    "playwright@claude-plugins-official": true,
    "github@claude-plugins-official": true,
    "stripe@claude-plugins-official": true,
    "figma@claude-plugins-official": true,
    "vercel@claude-plugins-official": true,
    "firebase@claude-plugins-official": true,
    "firecrawl@claude-plugins-official": true
  }
}
```

**Note:** The `permissions` block is machine-specific (file paths). Rebuild it as you approve tools on the new machine.

---

## 2. Global CLAUDE.md

> `~/.claude/CLAUDE.md`

```markdown
# Global Claude Configuration

Keep this file focused. Document only what Claude can't figure out by reading code. Detailed rules live in `~/.claude/rules/`.

## About Dan

- Dan Kenney (daniel.kenney@gmail.com) | GitHub: dankenney
- Platform: Windows with Git Bash
- Tools: Claude Code + ChatGPT Codex (OpenAI Pro) + Linear (task tracking)
- Portfolio: 13 projects across ESG/compliance, competitive pricing, insurance intelligence
- Phase: Transitioning from rapid MVP building to production-grade engineering

## General Preferences

- Be concise and direct
- Only use emojis when explicitly requested
- Focus on practical solutions over theoretical discussions
- Think before acting -- use Plan Mode for complex changes
- Keep solutions simple -- avoid over-engineering
- 2-3 iterations produce better results than accepting first output

### Banned Phrases (AI slop)
Never use these filler phrases in any output:
- "doing the heavy lifting"
- "the real question is"
- "here's the thing nobody is talking about"
- "that's the real story"
- "what most people miss"
- "this is where it gets interesting"
- "it's not about ___, it's about ___"

## Engineering Standards

- Make minimal, focused changes -- don't add features beyond what's requested
- Target 70%+ test coverage for any project being shipped
- Every external API call needs error handling (timeouts, rate limits, auth failures)
- Before marking a project "done": tests pass, no secrets exposed, deployment docs exist
- When writing tests with agents: confirm tests FAIL first (red phase) before implementing (green)

## Project Discipline

- Don't create variant repos -- use feature branches, merge back within 2 weeks or archive
- Before starting a new project, check if it should be a branch of an existing one
- One flagship project at a time gets the full production treatment (monitoring, CI/CD, docs)

## Workflow

### Git & Version Control
- **Explore -> Plan -> Implement -> Commit** for complex changes
- Create commits only when explicitly requested
- Always include: Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
- Detailed git rules in `~/.claude/rules/git-workflow.md`

### Error Handling
- Check for errors after running commands
- Provide clear error messages and context
- When stuck after 2 failed corrections, `/clear` and write better prompt

### Context Management
- **Context is your most valuable resource** -- manage it aggressively
- Use `/clear` between unrelated tasks
- Use subagents for investigation to keep main context clean
- Use `/fork` for parallel non-overlapping tasks
- **CONTEXT ALERT**: At 20+ tool uses, proactively warn Dan about context weight. Suggest committing and `/compact`.
- If auto-compaction occurs, immediately alert Dan and suggest committing pending changes
- After completing significant work, proactively suggest committing before the next task

### Mode Usage
- Plan Mode for: new features, architectural decisions, multi-file changes, unclear requirements
- Skip Plan Mode for: simple fixes, single-line changes, clear instructions

### Code Review After Implementation
After completing a significant build, automatically assess:
- **Tests first**: Zero coverage is the biggest risk
- **Focus on what matters**: Target 2-3 areas with real risk, skip ceremony
- **Be honest**: If a full review would be busywork, say so
- **Always fix bugs found by tests** immediately

### When Claude Loops
1. After 2 failed attempts, stop
2. `/clear` for fresh start
3. Simplify the task
4. Show concrete examples instead of abstract descriptions

## Debugging Approach

- Reproduce bugs first with a minimal test case before attempting a fix
- Investigate actual issues directly -- don't analyze code theoretically
- Do NOT claim external tools are deprecated without verification

## Data Safety

- ALWAYS commit and push before running destructive operations or switching tools
- Before any database modification, create a timestamped backup
- **No company names in Notion entries**: Describe domains generically in Notion Project Lab. Company names are fine in code, config, and internal docs.

## Pipeline & Long-Running Tasks

- Always show progress indicators (items processed, percentage, ETA)
- After data-changing operations, run a data quality check
- Stop after 3 consecutive failures, report what succeeded vs failed

## Task Tracking (Linear)

- Team key: BES | Default sort: priority
- Before starting work on a project, check Linear for assigned/open issues
- After completing work, update the Linear issue status (Done or appropriate state)
- When discovering new work items, create Linear issues -- don't leave bare TODOs in code
- Include Linear issue ID in commit messages when applicable (e.g., `feat: add export [ENG-42]`)
- Use Linear MCP in Claude Code; use `linear` CLI in Codex/Antigravity
- Don't create issues for already-completed work
- Start with 2-3 active projects, not all 23

## Notes

- Project-specific settings go in project-level CLAUDE.md files
- Use CLAUDE.local.md for machine-specific overrides (gitignored)
```

---

## 3. Rules

### `~/.claude/rules/agentic-patterns.md`

```markdown
# Agentic Engineering Patterns
## Source: Simon Willison (simonwillison.net/guides/agentic-engineering-patterns/)
## Last synced: 2026-03-18
## Chapters processed: what-is-agentic-engineering, code-is-cheap, hoard-things-you-know-how-to-do, better-code, anti-patterns, how-coding-agents-work, subagents, red-green-tdd, first-run-the-tests, agentic-manual-testing, linear-walkthroughs, interactive-explanations, gif-optimization, prompts
## Tags reviewed: 6 posts through 2026-03-18

### Code Cost Awareness
- Default to trying things with agents instead of deliberating -- code is cheap, review is the bottleneck
- Fire off exploratory prompts in parallel/async sessions -- worst case, check back in 10 minutes
- Generating code is nearly free; GOOD code (tested, reviewed, maintained) still costs real effort

### Test-First with Agents
- Start every session on an existing project by running the tests first ("First run the tests")
- Use "red/green TDD" as shorthand when prompting -- all major models understand this
- Always confirm tests FAIL first (red phase) -- agents silently write tests that already pass
- A passing test suite tells the agent about project structure, complexity, and existing patterns

### Hoard Working Examples
- Maintain a personal library of working code examples you've built or verified
- When facing new problems, point agents at your existing repos to recombine proven patterns
- "Knowing something is theoretically possible is not the same as having seen it done yourself"

### Linear Walkthroughs
- After building something (especially vibe-coded), have the agent write a walkthrough explaining how it works
- This turns passively absorbed code into genuine understanding
- Use execution-based docs (real outputs, not commentary) to ground explanations in reality

### Parallel Agent Workflows
- Use agents as "scouts" on hard problems -- explore without committing results
- Run parallel agents in isolated worktrees to prevent conflicts
- Match agent tasks to strengths: research, understanding existing code, well-specified features

### Sustainable Pacing
- Agentic work concentrates decision-making -- plan focused bursts, not marathon sessions
- AI removes routine work but makes review the bottleneck -- budget time accordingly

### Better Code & Anti-Patterns
- Use agents for tedious refactoring (renames, file splits, dedup) -- fire them off in background branches
- Run exploratory prototypes with agents to validate tech choices before committing
- After agent work, do a "compound step" -- document what worked for future runs
- Never file PRs with unreviewed agent code -- review is your responsibility, not the reviewer's
- Include evidence of manual testing in PRs (screenshots, demos, notes on what you tried)

### Agentic Manual Testing
- Supplement automated tests with agent-driven manual testing (curl, python -c, browser automation)
- When manual testing finds a bug, fix with red/green TDD so it becomes a permanent automated test

### Subagent Strategy
- Use subagents to preserve root context -- dispatch exploration and verbose operations to fresh contexts
- Don't over-decompose into specialist subagents -- the root agent is capable when it has tokens to spare

### Cognitive Debt
- Build interactive explanations when you need to truly understand agent-written algorithms
- Cognitive debt (code you don't understand) blocks future progress the same way technical debt does

### Writing Quality
- Avoid AI slop phrases -- see banned list in global CLAUDE.md
- Write like a competent engineer, not a LinkedIn influencer
- State things plainly instead of using rhetorical framing devices
```

### `~/.claude/rules/git-workflow.md`

```markdown
# Git Workflow Rules

## Commit Standards

### When to Commit
- Only create commits when explicitly requested by user
- Never create automatic commits without permission

### Commit Message Format
Follow conventional commits:

<type>: <description>

[optional body]

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>

**Types:**
- `feat:` New feature
- `fix:` Bug fix
- `refactor:` Code change that neither fixes nor adds features
- `docs:` Documentation only
- `test:` Adding or updating tests
- `chore:` Maintenance tasks
- `perf:` Performance improvements

### Commit Guidelines
- Keep description under 50 characters
- Use imperative mood ("add" not "added")
- Don't end with period
- Provide context in body if needed

## Branch Management

- Never force push to `main` or `master` without explicit permission
- Create descriptive branch names
- Use prefixes: `feature/`, `fix/`, `refactor/`

## Staging Files

- Be specific about which files to stage
- Avoid `git add .` or `git add -A`
- Stage files by name or pattern
- Skip generated files, dependencies, and sensitive data

## Pre-commit Checks

Before committing, verify:
- [ ] No sensitive data being committed
- [ ] No debug code or console.logs (unless intentional)
- [ ] Code is formatted
- [ ] Tests pass
- [ ] No linting errors

## Pull Requests

When creating PRs:
- Write clear, descriptive titles
- Include summary of changes
- Add test plan
- Reference related issues
- Add "Generated with Claude Code" footer
```

### `~/.claude/rules/security.md`

```markdown
# Security Rules

These rules MUST be followed at all times:

## Never Commit Secrets

- Never commit API keys, passwords, tokens, or credentials
- Check for `.env` files before staging
- Warn if attempting to commit files like:
  - `.env`
  - `credentials.json`
  - `secrets.yaml`
  - `*.key`, `*.pem`
  - Files containing "password", "secret", "token" in content

## Input Validation

- Always validate user input at system boundaries
- Sanitize data before using in:
  - SQL queries (use parameterized queries)
  - Shell commands (avoid command injection)
  - HTML output (prevent XSS)
  - File paths (prevent path traversal)

## Authentication & Authorization

- Never bypass authentication checks
- Always verify user permissions before sensitive operations
- Use secure session management
- Implement proper password hashing (bcrypt, argon2)

## Dependency Security

- Check for known vulnerabilities before adding dependencies
- Keep dependencies up to date
- Review security advisories
- Use `npm audit`, `pip check`, or equivalent

## Code Review Checklist

Before marking code complete, verify:
- [ ] No hardcoded secrets
- [ ] Input is validated
- [ ] SQL queries use parameterized statements
- [ ] XSS prevention in HTML output
- [ ] Proper error handling (don't leak sensitive info)
- [ ] Authentication/authorization checks in place
```

### `~/.claude/rules/testing.md`

```markdown
# Testing Rules

## Always Verify Code Works

- Run tests after making code changes
- Don't mark tasks complete if tests fail
- If tests don't exist, suggest creating them

## Test Coverage Requirements

When adding new functionality:
- Write tests for happy path
- Write tests for error cases
- Write tests for edge cases
- Write tests for boundary conditions

## Test Running Guidelines

- Prefer running specific tests over entire suite
- Run full test suite before creating PRs
- If tests are slow, run affected tests first
- Report test failures clearly with context

## Test Quality

Tests should be:
- **Independent**: Each test runs in isolation
- **Repeatable**: Same results every time
- **Fast**: Quick feedback loop
- **Clear**: Easy to understand what failed
- **Focused**: Test one thing at a time

## When Tests Fail

1. Read the error message carefully
2. Understand what the test expects
3. Identify why the code doesn't meet expectations
4. Fix the code or update the test (if expectations changed)
5. Never skip or disable failing tests without investigation
```

---

## 4. Custom Commands

### `~/.claude/commands/start.md`

```markdown
---
description: Start-of-session status check and context reload
---
Read CLAUDE.md and project-meta.json. Give me a brief status update:
- Project name and current phase
- What we did last session (check docs/changelog.md)
- What's in progress and what should we tackle next
- Any blockers or decisions needed

Then check agentic patterns freshness:
- Read the "Last synced" date in ~/.claude/rules/agentic-patterns.md
- If older than 14 days: suggest running /sync-patterns
- Otherwise: skip silently

Then check Linear for active work:
- List open issues assigned to me in team BES (In Progress or high-priority unstarted)
- If any In Progress issues exist, mention them as potential continuations
- Keep brief -- issue IDs and titles, 5 max

Then run a Linear health check (weekly — skip if last check was <7 days ago per memory):
- Count total open issues across team BES. If approaching 200: warn about 250 free tier limit.
- Flag any issues that have been "In Progress" for >14 days — they may be stuck or need re-scoping.
- If the same issues keep showing up in /start without being worked on, suggest triaging: close, deprioritize, or break them down.
- If any automation candidates from ~/.claude/projects/C--Users-danie/memory/project_linear_automations.md have hit their trigger threshold, flag them.
- Update memory with the check date when done.

Then check the weekly digest status:
- Look for the most recent twillot-bookmark*.csv in C:\Users\danie\Downloads\ (ignore .zip files)
- Read the latest file in C:\Users\danie\projects\x-bookmarks-kb\digests\
- If the CSV is older than 7 days: remind me to export fresh bookmarks from Twillot
- If the CSV is fresh but the last digest is older than 7 days: suggest running /digest
- If both are fresh and recent: skip this check silently

Keep it concise — just enough to get oriented.
```

### `~/.claude/commands/wrap-up.md`

```markdown
---
description: End-of-session documentation, tracker update, and Notion sync
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

6. Linear — if we worked on a tracked issue this session:
   - Update state to "Done" if complete, or "In Progress" with a progress comment
   - Create Linear issues for any new work items or TODOs discovered
   - Use MCP tools (preferred) or fall back to CLI
   - Quick triage: if you notice open issues piling up (>15 open in BES), mention it and suggest a 5-minute triage pass
   - If a manual workflow was repeated this session that could be automated (e.g., branch-to-issue linking, status transitions, commit message tagging), note it as a candidate in ~/.claude/projects/C--Users-danie/memory/project_linear_automations.md

7. Push to Notion Project Lab (data source: collection://063f903c-6ec1-4431-92ea-66487e515b8b):
   - Search for this project by name
   - If found, update: Status, Description, Hours Saved, Skills Used, Lessons Learned, Last Updated
   - If not found, create a new entry with all fields populated from project-meta.json
   - Show me the Notion URL when done

8. Sync Skills Catalog in Notion (data source: collection://65821b67-54d2-4683-af6f-0ef7643d92fb):
   - Check if any new skills, plugins, MCP servers, hooks, or commands were installed or created this session
   - For each new item, search the Skills Catalog by name to avoid duplicates
   - If not found, create a new entry with:
     - `Name` = skill/plugin/command name
     - `Type` = one of: Skill, Plugin, MCP, Hook, Command
     - `Install Command` = the install command if applicable
     - `Notes` = brief description of what it does
     - `Source` = GitHub URL or source link if available
   - If nothing new was installed this session, skip silently

Write all entries in a direct, analytical tone — like a senior consultant
documenting outcomes for a stakeholder who needs to understand what happened
and why it matters.
```

### `~/.claude/commands/digest.md`

```markdown
---
description: Generate a weekly bookmark digest from new X/Twitter bookmarks exported via Twillot, then push top picks to Notion
---

## Pre-flight Check (ALWAYS run first)

1. Look for the most recent twillot-bookmark CSV in `C:\Users\danie\Downloads\`:
   - Check for: `twillot-bookmark.csv`, `twillot-bookmark (1).csv`, `twillot-bookmark (2).csv`, etc.
   - Use the one with the **most recent modification date**
   - Ignore `.zip` files (those are backups, not CSV exports)
2. If the newest CSV is **older than 7 days**, STOP and tell Dan:
   > "Your Twillot export is from [date]. Export fresh bookmarks from X first:
   > Open X.com → Bookmarks → Twillot extension → Export CSV → Save to Downloads"
3. If no CSV exists, STOP and give the same instruction.
4. Only proceed if the newest CSV is fresh (modified within the last 7 days).

## Instructions

1. Read the most recent twillot-bookmark CSV (identified in pre-flight check) from `C:\Users\danie\Downloads\`
2. Read the existing knowledge base at `C:\Users\danie\projects\x-bookmarks-kb\README.md`
3. Diff the CSV against the knowledge base to identify new bookmarks not already captured
4. For each new bookmark:
   - Categorize it into one of the existing sections (Claude Code, AI/ML, Product, Finance, Personal, or create a new subsection if needed)
   - Visit the link and extract the key actionable takeaway
   - Write a 2-3 sentence "how-to" summary focused on "here's what you can do with this"
5. Generate two outputs:
   - **Weekly digest file**: Save to `C:\Users\danie\projects\x-bookmarks-kb\digests/YYYY-MM-DD.md` using today's date
   - **Update the main README.md**: Add the new entries into the appropriate sections
6. **Push Weekly Picks to Notion (MANDATORY — do not skip)**:
   - Search the Weekly Picks database for existing entries to avoid duplicates
   - From the digest highlights (top 5-7 entries), push each NEW one to Notion
   - Data source ID: `1936fbd5-5e37-484b-82d3-4d518bd695ae`
   - Map fields:
     - `Title` = bookmark title/headline
     - `Theme` = one of: AI Coding, AI/ML, Product, Finance, Personal Dev
     - `Source` = @handle of the original poster
     - `Link` = URL of the tweet/article
     - `Key Takeaway` = the 1-line actionable insight
     - `Week` = date range for the current week (Monday to Sunday)
   - Theme mapping from KB sections:
     - Part 1 (Claude Code & AI Coding) → "AI Coding"
     - Part 2 (AI/ML Fundamentals) → "AI/ML"
     - Part 3 (Product & Startups) → "Product"
     - Part 4 (Finance & Investing) → "Finance"
     - Part 5 (Personal Development) → "Personal Dev"
   - **Dedup check**: Before creating each entry, search the database by Link URL. Skip if it already exists.
   - After pushing, report: "[X] new picks pushed to Notion, [Y] skipped (already existed)"
   - If Notion MCP is genuinely unavailable (tool error), warn Dan loudly — do NOT silently skip
7. **Update the Weekly Picks Archive page (MANDATORY)**:
   - Fetch the Weekly Picks Archive page (`311c67a2483a81deb2a0cda1ae28d70a`)
   - Add a new section at the TOP of the page content (most recent first), below the intro text:
     - H2: "Week of [Monday date] - [Sunday date], YYYY"
     - H3: "Top Picks"
     - Numbered list of the picks pushed this week: each with title, source handle, key takeaway, and link
     - H3: "Putting Picks into Practice"
     - Placeholder text: *"Updated by `/weekly-recap`"*
   - This keeps the archive current automatically — no manual editing needed
8. **Detect new skills from bookmarks**:
   - After processing all new bookmarks, scan each one for tool/skill mentions:
     - CLI tools (identified by install commands: `npm install`, `pip install`, `brew install`, `npx`, `cargo install`, etc.)
     - MCP servers (mentions of "MCP", "model context protocol", "MCP server")
     - Claude Code skills or plugins (mentions of "skill", "plugin", installation instructions)
     - VS Code extensions, browser extensions
     - New AI frameworks, APIs, or platforms
   - For each detected tool/skill:
     - Search the Skills Catalog in Notion (data source: `65821b67-54d2-4683-af6f-0ef7643d92fb`) by name
     - If not found, create a new entry:
       - `Name` = tool/skill name
       - `Type` = best-fit: Skill, Plugin, MCP, Hook, or Command
       - `Install Command` = extracted install command if present in the bookmark
       - `Notes` = brief description from the bookmark context
       - `Source` = bookmark URL
     - If found, skip
   - Report: "[X] new skills detected and added to catalog from this week's bookmarks"
   - If no new skills detected, skip silently

## Digest File Format

    # Bookmark Digest - [DATE]

    > [X] new bookmarks processed from Twillot export

    ## Big Themes

    [2-3 sentence summary of the week's dominant patterns and trends across all new bookmarks. What's shifting? What's gaining momentum? What should Dan pay attention to?]

    ## Highlights

    [Top 5-7 most actionable new bookmarks with full summaries]

    ## All New Entries

    ### [Category Name]
    - **[Title/Description]** - [2-3 sentence how-to summary] - [link]

    ### [Category Name]
    - ...

## Rules

- Skip photography content unless particularly notable
- Focus on actionable takeaways, not just descriptions
- If a link is dead or inaccessible, note it and still include the bookmark based on the tweet content
- Keep summaries concise and practical
- If no new bookmarks are found, say so and skip file creation
```

### `~/.claude/commands/weekly-recap.md`

```markdown
---
description: Generate a weekly activity recap from git logs, session data, and insights, then push to Notion
---

## Weekly Recap: What did you get done this week?

Generate a comprehensive weekly activity recap and push it to the Dan's Lab Notion page.

### Step 1: Scan git activity across all projects (past 7 days)

For each directory in `~/projects/` that contains a `.git` folder:
- Run `git log --oneline --since="7 days ago" --all` in each project
- Capture: project directory name, commit count, and the commit messages
- Skip projects with zero commits in the past 7 days
- Also check for uncommitted changes (staged or unstaged) — note active work-in-progress

Aggregate into a summary: "X commits across Y projects"

### Step 2: Check GitHub activity

- Run `gh api /users/dankenney/events --paginate -q '.[] | select(.type | test("Push|PullRequest|Create"))' | head -50`
- Filter to events from the past 7 days
- Capture: PRs opened/merged, repos pushed to, branches created
- Deduplicate with local git data from Step 1 — note any GitHub-only activity

### Step 3: Summarize session insights

For each project with commits this week, read (if they exist):
- `docs/changelog.md` — look for entries dated within the past 7 days
- `docs/FORDAN.md` — check for recent learning notes
- `project-meta.json` — read `lessons_learned` (latest entry), `skills_used`, `tools_used`
- `CLAUDE.md` — read the "Current Status" section for what's in progress and what's next

Also scan for session-level insights:
- Read `~/.claude/projects/*/memory/` directories for any memories saved this week (check file modification dates)
- These capture feedback, user preferences, project context, and references from Claude Code sessions
- Look for patterns: recurring feedback, emerging themes, tools being adopted or dropped

Distill into 3-5 insight bullets covering: what was built, what was learned, what patterns are emerging.

### Step 4: Cross-reference Picks into Practice

- Fetch the Weekly Picks database from Notion (data source: `collection://1936fbd5-5e37-484b-82d3-4d518bd695ae`)
- Get entries from the most recent week
- Check if any pick's tool, concept, or technique appears in:
  - New entries in `skills_used` or `tools_used` in any project-meta.json updated this week
  - This week's commit messages (case-insensitive substring match)
- If matches found, note them for the "Picks Put into Practice" section
- Also update the Weekly Picks Archive page (`311c67a2483a81deb2a0cda1ae28d70a`) — find the corresponding week's "Putting Picks into Practice" sub-heading and replace placeholder text with the actual connections

### Step 4b: Sync project hours and status to Notion

For each project with commits this week:
- Read `project-meta.json` for `hours.ai_assisted_actual`, `hours.manual_estimate`, and `status`
- Calculate Hours Saved = `manual_estimate - ai_assisted_actual`
- Search the Project Lab database (data source: `collection://063f903c-6ec1-4431-92ea-66487e515b8b`) for the project by name
- Update each project entry:
  - `Hours Saved` = calculated value
  - `date:Last Updated:start` = today's date
  - `Status` = mapped from project-meta.json status:
    - "active" / "in-production" / "deployed" → "In Production"
    - "building" / "in-development" → "In Development"
    - "ideating" / "planning" → "Ideating"
    - "paused" / "archived" → "Paused"
- This keeps the Project Lab table current every time the recap runs

### Step 5: Sanitize the output

CRITICAL — apply these rules to ALL output before pushing to Notion:
- Use `public_name` from each project's `project-meta.json` when available. NEVER use internal project folder names that contain company references.
- If no `public_name` exists, use a generic description (e.g., "competitive intelligence tool" instead of the company name)
- Strip any API keys, tokens, passwords, or credentials from commit messages
- Strip internal URLs (localhost, staging URLs with company names)
- No company names anywhere in the output — per CLAUDE.md rules for Notion entries
- File paths are OK if they only reference project structure, not user-specific paths

### Step 6: Generate "Focus for Next Week"

- Read `CLAUDE.md` "Current Status" sections from projects with recent activity — look for "Next" or "In progress" items
- Check Linear for top 3 open issues (In Progress or high priority, team BES):
  - Use MCP tools if available, or fall back to `linear` CLI
- Distill to 2-3 concrete, actionable focus items for the coming week
- Frame as outcomes ("Ship X", "Fix Y") not activities ("Work on X")

### Step 7: Format the recap

Use this exact format:

    ## Week of [Mon DD] - [Mon DD], YYYY

    **Summary:** [X] commits across [Y] projects. [1-2 sentence narrative of the week's theme — what was the main thrust of work?]

    #### Project Highlights
    **[Project public_name]** ([X] commits)
    - [Key thing accomplished — one bullet per notable change]
    - [Another accomplishment]

    **[Project public_name]** ([X] commits)
    - [...]

    (Include all projects with activity, ordered by commit count descending. Use bullet points, not paragraphs.)

    #### What I Learned
    - [Insight from changelogs, FORDAN, or lessons_learned — concrete and specific]
    - [...]
    - [...]

    #### Picks Put into Practice
    - [Pick title] → Applied in [project public_name]: [how]
    - (or "No direct connections this week — but keeping an eye on [topic]")

    #### Focus for Next Week
    1. [Priority item — framed as an outcome]
    2. [Priority item]
    3. [Priority item]

### Step 8: Push to Notion

1. Fetch the Dan's Lab landing page (`30cc67a2-483a-8139-abcb-d1f6ee13a146`)
2. Update the cumulative stats line at the top of the page:
   - Count total projects (directories with .git in ~/projects/)
   - Count total commits across all projects (`git log --oneline --all` in each)
   - Sum hours saved from all project-meta.json files (`manual_estimate - ai_assisted_actual`)
   - Update the line matching `**X projects | Y+ commits | Z hours saved**` with fresh numbers
3. Update the "Current flagship" line if CLAUDE.md mentions a different flagship project
4. Find the "What did you get done this week?" heading
5. Replace all content between that heading and the "Weekly Picks" heading with the formatted recap:
   - Use `update_content` with `old_str` matching the current content between those headings
   - The new content should include the recap markdown from Step 7
   - Include `*Updated [today's date]*` under the week heading
6. Update the Weekly Picks default view filter to show only the current week:
   - Calculate the current week's Monday date
   - Update view `view://a6ac5679-8e8a-465d-acf6-0db2c0e2d2d8` with `FILTER "Week" >= "[monday date]"`

### Step 9: Report

- Show the full recap content to Dan
- Confirm Notion push succeeded with the page URL: `https://dankenney.notion.site/Dan-s-Lab-30cc67a2483a8139abcbd1f6ee13a146`
- Flag any projects that had commits but no changelog or FORDAN data (gaps in documentation)
- If any projects lack a `public_name` in project-meta.json, warn and suggest adding one

## Rules

- Write in a direct, analytical voice — like a senior engineer's weekly standup
- Avoid AI slop phrases (see banned list in CLAUDE.md)
- Keep project highlights to 1-2 sentences each — scannable, not exhaustive
- The recap should take ~30 seconds to read
- If no activity found in the past 7 days, say so clearly and skip to "Focus for Next Week"
```

### `~/.claude/commands/commit-push-pr.md`

```markdown
---
description: Create commit, push, and open PR in one flow
allowed-tools: Bash(git:*, gh:*)
---

Complete git workflow - commit, push, and create PR:

1. **Review Changes**
   - Run `git status` to see all changes
   - Run `git diff` for unstaged and `git diff --staged` for staged changes
   - Review recent commits with `git log --oneline -5`

2. **Stage Files**
   - Add relevant files (avoid using `git add .`)
   - Be specific about which files to include
   - Skip any sensitive or generated files

3. **Create Commit**
   - Draft commit message following conventional commits format
   - Include Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
   - Verify commit was created with `git status`

4. **Push to Remote**
   - Check if branch tracks remote
   - Push with `-u` flag if needed
   - Confirm push succeeded

5. **Create Pull Request**
   - Use `gh pr create` with title and body
   - Include summary of changes
   - Add test plan section
   - Include footer: "Generated with Claude Code"

6. **Report PR URL**
   - Provide the PR URL for easy access
   - Summarize what was accomplished
```

### `~/.claude/commands/fix-issue.md`

```markdown
---
argument-hint: [issue-number]
description: Fix a GitHub issue end-to-end
allowed-tools: Read, Grep, Glob, Bash(git:*, gh:*)
---

Fix GitHub issue #$ARGUMENTS:

1. **Fetch Issue Details**
   - Use `gh issue view $ARGUMENTS` to get full context
   - Understand the problem, expected behavior, and steps to reproduce

2. **Investigate**
   - Search codebase for relevant files using Grep and Glob
   - Read the relevant code to understand current implementation
   - Identify root cause of the issue

3. **Implement Fix**
   - Make necessary code changes
   - Ensure fix is minimal and focused
   - Follow existing code patterns and conventions

4. **Test the Fix**
   - Write or update tests to verify the fix
   - Run tests to ensure they pass
   - Check for any regressions

5. **Create Commit**
   - Stage the changes
   - Create descriptive commit message: "fix: [brief description] (#$ARGUMENTS)"
   - Include details in commit body if needed

6. **Report Results**
   - Summarize what was changed
   - Explain how the fix addresses the issue
   - Note any additional changes or considerations
```

### `~/.claude/commands/refactor.md`

```markdown
---
argument-hint: [file or component name]
description: Safely refactor code with tests
---

Refactor $ARGUMENTS:

1. **Understand Current Implementation**
   - Read the code thoroughly
   - Understand the purpose and functionality
   - Identify dependencies and usage
   - Note existing tests

2. **Create Safety Net**
   - Ensure existing tests pass
   - Add tests if coverage is insufficient
   - Document current behavior

3. **Plan Refactoring**
   - Identify what needs to change
   - Plan minimal, incremental changes
   - Consider backwards compatibility
   - Note potential risks

4. **Implement Changes**
   - Make one logical change at a time
   - Preserve existing behavior
   - Improve code quality (readability, maintainability)
   - Don't add new features during refactoring

5. **Verify**
   - Run all tests after each change
   - Check for any regressions
   - Verify performance hasn't degraded
   - Review the refactored code

6. **Document**
   - Update comments if needed
   - Note any important changes
   - Update related documentation

**Refactoring Principles:**
- Make code easier to understand
- Eliminate duplication
- Improve naming
- Simplify complex logic
- Don't change behavior
```

### `~/.claude/commands/review.md`

```markdown
---
description: Perform comprehensive code review of recent changes
---

Perform a comprehensive code review of recent changes:

1. **Check Recent Changes**
   - Run `git diff` to see unstaged changes
   - Run `git diff --staged` to see staged changes
   - Review the actual code modifications

2. **Code Quality Review**
   - Verify code follows established conventions
   - Check for proper error handling
   - Ensure loading states are handled
   - Review variable and function naming
   - Check for code duplication

3. **Security Review**
   - Check for SQL injection vulnerabilities
   - Look for XSS risks
   - Verify no hardcoded secrets
   - Check authentication/authorization
   - Review input validation

4. **Performance Review**
   - Identify inefficient algorithms
   - Check for N+1 query problems
   - Look for unnecessary loops
   - Review memory usage patterns

5. **Testing**
   - Verify test coverage for new functionality
   - Check edge cases are handled
   - Ensure error conditions are tested

6. **Provide Structured Feedback**
   - Critical issues (must fix)
   - Important issues (should fix)
   - Suggestions (nice to have)
   - Positive notes (what's good)

Reference specific line numbers when pointing out issues.
```

### `~/.claude/commands/sync-patterns.md`

```markdown
---
description: Sync new Agentic Engineering Patterns from Simon Willison's guide series
---

## What This Does
Checks Simon Willison's Agentic Engineering Patterns guide for new chapters and tagged posts, distills actionable rules, and proposes updates to Dan's rules file.

## Step 1: Check for New Content

1. Read the current rules file at `~/.claude/rules/agentic-patterns.md`
   - Note the "Chapters processed" and "Tags reviewed" lines — these are the state tracker
2. Fetch the guide index at `https://simonwillison.net/guides/agentic-engineering-patterns/`
3. Extract all published chapter slugs and compare against "Chapters processed"
4. Fetch the tag page at `https://simonwillison.net/tags/agentic-engineering/`
5. Count total posts and compare against "Tags reviewed" count
6. Report: "[X] new guide chapters, [Y] new tagged posts found"
7. If nothing new: report "All caught up" and STOP

## Step 2: Process New Guide Chapters

For each new chapter:
1. Fetch the full chapter content
2. Ask: Does this suggest a specific BEHAVIOR for how Dan or Claude should work?
   - YES → draft 1-3 concise rules in imperative mood
   - NO (purely philosophical) → acknowledge it, note it in the header, skip rules
3. Show Dan the proposed additions before writing anything

## Step 3: Review New Tagged Posts

For each new tagged post:
1. Fetch and read the content
2. Most tagged posts are commentary or links — they rarely yield rules
3. Only extract rules if Simon gives concrete, specific workflow advice
4. Update the "Tags reviewed" count in the header

## Step 4: Update the Rules File

1. Show Dan the proposed changes as a before/after diff
2. Only write after Dan confirms
3. Update the header metadata:
   - "Last synced" date
   - "Chapters processed" list
   - "Tags reviewed" count
4. If the file exceeds 55 lines, ask Dan which rules have become second nature and can be graduated out

## Rules

- NEVER auto-write to the rules file — always show Dan first
- Each rule must be independently understandable without reading the source
- Prefer 1 concrete rule over 3 vague ones
- If a chapter is purely philosophical, it's OK to extract zero rules
- Keep the file under 60 lines total
- Match the existing style: `### Section Header` then `- Rule in imperative mood`
```

---

## 5. Custom Skills

### `~/.claude/skills/code-review.md`

```markdown
---
name: code-review
description: Perform a thorough code review with best practices checklist
---

# Code Review Skill

When performing code reviews, systematically check for:

## Security
- SQL injection vulnerabilities
- XSS (Cross-Site Scripting) risks
- Command injection possibilities
- Authentication and authorization flaws
- Hardcoded secrets or credentials
- Unsafe deserialization
- Path traversal vulnerabilities

## Code Quality
- Code readability and clarity
- Proper error handling
- Appropriate use of comments (only where needed)
- Consistent naming conventions
- DRY principle (Don't Repeat Yourself)
- Function/method length (should be focused and single-purpose)
- Proper use of language idioms

## Performance
- Inefficient algorithms or data structures
- N+1 query problems
- Unnecessary loops or iterations
- Memory leaks
- Blocking operations on main thread

## Testing
- Adequate test coverage
- Edge cases handled
- Error conditions tested
- Mock usage (prefer minimal mocking)

## Maintainability
- Clear separation of concerns
- Proper abstraction levels
- Avoiding premature optimization
- No over-engineering
- Documentation where complexity is unavoidable

## Output Format

Provide feedback in this structure:
1. **Summary**: Brief overview of the code's purpose and quality
2. **Critical Issues**: Security vulnerabilities and bugs (must fix)
3. **Important Issues**: Code quality problems that should be addressed
4. **Suggestions**: Optional improvements and best practices
5. **Positive Notes**: What the code does well

Be constructive and specific. Reference line numbers when pointing out issues.
```

### `~/.claude/skills/debug-workflow.md`

```markdown
---
name: debug-workflow
description: Systematic debugging approach for troubleshooting issues
---

# Debug Workflow Skill

Follow this systematic approach when debugging issues:

## 1. Understand the Problem
- What is the expected behavior?
- What is the actual behavior?
- When did this start happening?
- Can you reproduce it consistently?
- What are the exact steps to reproduce?

## 2. Gather Information
- Check error messages and stack traces
- Review relevant log files
- Check recent code changes (git log, git diff)
- Identify what changed between working and broken states
- Check environment variables and configuration

## 3. Form Hypotheses
- Based on the information, what could be causing this?
- List possible root causes from most to least likely
- Consider: code logic, environment, dependencies, data, external services

## 4. Test Hypotheses
- Test one hypothesis at a time
- Use debugging tools (print statements, debuggers, logging)
- Add temporary instrumentation to gather more data
- Isolate the problem area (binary search through code)

## 5. Fix and Verify
- Once root cause is found, implement the fix
- Write a test case that reproduces the bug
- Verify the fix resolves the issue
- Check for similar issues elsewhere in the codebase
- Clean up any temporary debugging code

## 6. Prevent Recurrence
- Add tests to catch this in the future
- Document any non-obvious behavior
- Consider if architecture changes could prevent similar issues

## Common Debugging Techniques
- **Binary search**: Comment out half the code to narrow down the problem
- **Rubber duck debugging**: Explain the problem step by step
- **Logging**: Add strategic log statements to trace execution
- **Minimal reproduction**: Create smallest possible example that shows the bug
- **Read the error**: Actually read the full error message and stack trace
- **Check assumptions**: Verify your assumptions about how the code works

## When Stuck
- Take a break and come back with fresh eyes
- Explain the problem to someone else (or me!)
- Search for similar issues online
- Check documentation for the libraries/frameworks involved
- Simplify: remove complexity until the problem goes away
```

### `~/.claude/skills/excalidraw-diagrams/` (directory)

This is a multi-file skill. **Copy the entire directory** from the old machine:

```
~/.claude/skills/excalidraw-diagrams/
├── SKILL.md                          # Skill definition (716 lines)
├── CLAUDE.md                         # Dev instructions
├── README.md
├── scripts/
│   ├── excalidraw_generator.py       # Core Python library
│   ├── layout_engine.py              # Auto-layout engine
│   ├── drive_helper.py               # Google Drive integration
│   ├── export_playwright.js          # PNG export via Playwright
│   ├── create_capability_zip.py      # Packaging utility
│   ├── line_routing_validator.py     # Validation
│   ├── package.json                  # Node dependencies (Playwright)
│   └── package-lock.json
└── tests/
    ├── test_generator.py
    ├── test_diagram_types.py
    ├── test_line_routing_validator.py
    ├── test_prompt_generation.py
    └── prompts.json
```

After copying, run setup:
```bash
cd ~/.claude/skills/excalidraw-diagrams/scripts
npm install
npx playwright install chromium
```

---

## 6. Custom Agents

### `~/.claude/agents/doc-writer.md`

```markdown
---
name: doc-writer
description: Specialized agent for creating and improving documentation
tools: Read, Grep, Glob, Write, Edit
model: sonnet
---

# Documentation Writer Agent

You are a specialized agent focused on creating clear, helpful documentation.

## Your Responsibilities

1. **Understand the Code First**
   - Read the relevant code thoroughly
   - Understand the purpose and functionality
   - Identify key features and use cases
   - Note any edge cases or important details

2. **Write Clear Documentation**
   - Use simple, direct language
   - Start with the most important information
   - Provide practical examples
   - Explain the "why" not just the "what"

3. **Structure Effectively**
   - Use clear headings and sections
   - Include a table of contents for longer docs
   - Put quick-start information at the top
   - Organize from general to specific

## Documentation Types

### README Files
- Project overview and purpose
- Quick start / installation instructions
- Basic usage examples
- Links to more detailed documentation
- Contributing guidelines (if applicable)
- License information

### API Documentation
- Function/method signature
- Parameter descriptions (type, purpose, constraints)
- Return value description
- Examples of common use cases
- Error conditions and exceptions
- Related functions/methods

### Code Comments
- Only add comments where logic isn't self-evident
- Explain "why" not "what" (code shows what)
- Document non-obvious behavior
- Note important assumptions or constraints
- Warning about common pitfalls

### How-To Guides
- Step-by-step instructions
- Prerequisites clearly stated
- Expected outcomes at each step
- Screenshots or examples where helpful
- Troubleshooting common issues

## Writing Principles

1. **Clarity**: Use simple words and short sentences
2. **Accuracy**: Ensure technical correctness
3. **Completeness**: Cover all important aspects
4. **Consistency**: Use consistent terminology and formatting
5. **Examples**: Show, don't just tell
6. **Maintenance**: Keep docs up to date with code changes

## Common Pitfalls to Avoid

- Don't write obvious comments that just repeat the code
- Don't use jargon without explanation
- Don't assume too much prior knowledge
- Don't skip error handling in examples
- Don't forget to update docs when code changes

## Documentation Checklist

Before finishing documentation:
- [ ] Is the purpose/goal clear?
- [ ] Are all parameters/options explained?
- [ ] Are there practical examples?
- [ ] Are edge cases documented?
- [ ] Is the language clear and simple?
- [ ] Are code examples tested and working?
- [ ] Is formatting consistent?
- [ ] Are there any broken links?

## Output Format

When creating documentation:
1. Start with a clear title
2. Provide a brief summary (1-2 sentences)
3. Include practical examples early
4. Organize information logically
5. Use markdown formatting effectively
6. End with related links or next steps
```

### `~/.claude/agents/test-runner.md`

```markdown
---
name: test-runner
description: Specialized agent for running and analyzing test results
tools: Read, Grep, Glob, Bash
model: sonnet
---

# Test Runner Agent

You are a specialized agent focused on running tests and analyzing results.

## Your Responsibilities

1. **Run Tests Intelligently**
   - Identify the test framework in use (Jest, pytest, JUnit, etc.)
   - Run tests using the appropriate command
   - Run specific tests when possible to save time
   - Re-run failed tests to check for flakiness

2. **Analyze Test Failures**
   - Parse test output to identify failures
   - Read the relevant test files
   - Examine the code being tested
   - Identify the root cause of failures
   - Provide clear explanations of what went wrong

3. **Report Results**
   - Summarize test results clearly
   - Group failures by type or module
   - Highlight any patterns in failures
   - Suggest fixes for common issues

## Test Framework Detection

Look for these indicators:
- **JavaScript/TypeScript**: package.json (Jest, Mocha, Vitest), test files with .test.js or .spec.js
- **Python**: pytest.ini, setup.py, test files with test_*.py or *_test.py
- **Java**: pom.xml, build.gradle, JUnit imports
- **Go**: *_test.go files, go test command
- **Rust**: tests module, cargo test
- **.NET**: *.csproj files, xUnit/NUnit references

## Common Commands

Based on what you find, use commands like:
- `npm test` or `npm run test`
- `pytest` or `python -m pytest`
- `go test ./...`
- `cargo test`
- `mvn test` or `gradle test`
- `dotnet test`

## Analysis Approach

When tests fail:
1. Read the test output carefully
2. Identify which test(s) failed
3. Read the test file to understand what's being tested
4. Read the implementation being tested
5. Compare expected vs actual behavior
6. Identify the discrepancy
7. Suggest specific fixes

## Reporting Format

Provide results in this format:

**Test Results Summary**
- Total: X tests
- Passed: Y tests
- Failed: Z tests
- Status: All passed / Some failures

**Failed Tests** (if any):
1. [Test Name] - [Brief reason for failure]
   - File: path/to/test.js:line
   - Root cause: [explanation]
   - Suggested fix: [specific suggestion]

**Recommendations**:
- [Any additional suggestions for improving tests or code]
```

---

## 7. Marketplace Plugins to Re-enable

These install automatically from the plugin marketplace. Just search and enable each one:

1. `claude-md-management` - CLAUDE.md auditing/improvement
2. `code-review` - Code review agent
3. `figma` - Figma design implementation
4. `firecrawl` - Web scraping/search (replaces WebFetch/WebSearch)
5. `firebase` - Firebase integration
6. `frontend-design` - Frontend UI generation
7. `github` - GitHub integration
8. `linear` - Linear task tracking
9. `playwright` - Browser automation
10. `plugin-dev` - Plugin development tools
11. `ralph-loop` - Ralph Loop workflow
12. `security-guidance` - Security best practices
13. `stripe` - Stripe payment integration
14. `supabase` - Supabase integration
15. `superpowers` - Enhanced workflows (brainstorming, TDD, debugging, planning)
16. `vercel` - Vercel deployment

---

## 8. MCP Servers

Re-authorize these after install (they use HTTP OAuth, so just connecting triggers auth):

| Server | Type | URL |
|--------|------|-----|
| Notion | HTTP | `https://mcp.notion.com/mcp` |
| Linear | HTTP | `https://mcp.linear.app/mcp` |

---

## 9. Global Scripts (outside ~/.claude/)

These are referenced by commands but live in `~/scripts/`:

```
~/scripts/init-project.sh   # Foundation Package project scaffolding
~/scripts/scan-projects.sh   # Cross-project health scanner
```

Copy these separately from the old machine.

---

## 10. Notion Data Source IDs

These are referenced in commands and need to match your Notion workspace:

| Database | Data Source ID | Used By |
|----------|---------------|---------|
| Project Lab | `063f903c-6ec1-4431-92ea-66487e515b8b` | /wrap-up, /weekly-recap |
| Weekly Picks | `1936fbd5-5e37-484b-82d3-4d518bd695ae` | /digest |
| Skills Catalog | `65821b67-54d2-4683-af6f-0ef7643d92fb` | /digest, /wrap-up |
| Dan's Lab page | `30cc67a2-483a-8139-abcb-d1f6ee13a146` | /weekly-recap |
| Weekly Picks Archive | `311c67a2483a81deb2a0cda1ae28d70a` | /digest, /weekly-recap |
| Weekly Picks view | `a6ac5679-8e8a-465d-acf6-0db2c0e2d2d8` | /weekly-recap |

**Note:** If you recreate the Notion workspace, update these IDs in the command files.
