# Claude Code Setup Guide — New Computer

> Everything needed to recreate Dan's Claude Code environment from scratch.
> Generated: 2026-03-19

---

## Table of Contents

1. [Quick Start Checklist](#quick-start-checklist)
2. [Global Config: CLAUDE.md](#global-config-claudemd)
3. [Rules Files](#rules-files)
4. [Settings & Hooks](#settings--hooks)
5. [MCP Servers](#mcp-servers)
6. [Installed Plugins](#installed-plugins)
7. [Custom Skills](#custom-skills)
8. [Custom Commands (Global)](#custom-commands-global)
9. [Custom Commands (Project-Level)](#custom-commands-project-level)
10. [Excalidraw Diagrams Skill (Full)](#excalidraw-diagrams-skill)
11. [X Bookmarks Knowledge Base](#x-bookmarks-knowledge-base)

---

## Quick Start Checklist

```
1. Install Claude Code CLI
2. Copy ~/.claude/CLAUDE.md (Section 2)
3. Copy ~/.claude/rules/*.md (Section 3)
4. Copy ~/.claude/settings.json (Section 4)
5. Install plugins (Section 6) — run each `claude plugins install` command
6. Copy ~/.claude/skills/ directory (Section 7-8)
7. Copy ~/.claude/commands/ directory (Section 8)
8. Clone repos: cc-foundation-guide, x-bookmarks-kb
9. Authorize MCP servers: Notion, Linear (Section 5)
10. Install tooling: Git Bash, Node.js, Python, prettier, black
```

---

## Global Config: CLAUDE.md

**Location:** `~/.claude/CLAUDE.md`

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

## Rules Files

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

## Settings & Hooks

**Location:** `~/.claude/settings.json`

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
  "permissions": {
    "allow": [
      "Bash(mkdir:*)",
      "Bash(go:*)",
      "Write(*)",
      "Bash(ls:*)",
      "Bash(git:*)",
      "Update(*:*)",
      "Bash(mv:*)",
      "Bash(echo:*)",
      "Bash(sed:*)",
      "Bash(source:*)",
      "Bash(head:*)",
      "Bash(tail:*)",
      "Bash(npm:*)",
      "Bash(npx:*)",
      "Bash(pkill:*)",
      "Bash(touch:*)",
      "Bash(grep:*)",
      "Bash(deadcode:*)",
      "Bash(sqlite3:*)",
      "Bash(curl:*)",
      "Bash(rg:*)",
      "Bash(chmod:*)",
      "Bash(lsof:*)",
      "Bash(make:*)",
      "Bash(PORT=:*)",
      "Bash(find:*)",
      "Bash(docker:*)",
      "Bash(poetry:*)",
      "Bash(python:*)",
      "Bash(python3:*)",
      "Update(*)",
      "mcp__claude_ai_Notion__notion-update-data-source",
      "mcp__claude_ai_Notion__notion-create-pages",
      "mcp__claude_ai_Notion__notion-update-page",
      "Bash(git commit:*)",
      "mcp__claude_ai_Notion__notion-search",
      "mcp__claude_ai_Notion__notion-fetch",
      "Bash(wc:*)"
    ],
    "deny": []
  },
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

**Note:** The `permissions.allow` entries with file paths (e.g., `Read(//c/Users/danie/...)`) are machine-specific and will auto-accumulate as you approve tool use on the new machine. Don't copy those over.

---

## MCP Servers

Both are HTTP-based (no local install needed — just authorize on first use):

| Server | URL | Auth |
|--------|-----|------|
| Notion | `https://mcp.notion.com/mcp` | OAuth — authorize when prompted |
| Linear | `https://mcp.linear.app/mcp` | OAuth — authorize when prompted |

---

## Installed Plugins

Run these to reinstall all plugins:

```bash
claude plugins install claude-md-management@claude-plugins-official
claude plugins install frontend-design@claude-plugins-official
claude plugins install linear@claude-plugins-official
claude plugins install plugin-dev@claude-plugins-official
claude plugins install ralph-loop@claude-plugins-official
claude plugins install security-guidance@claude-plugins-official
claude plugins install supabase@claude-plugins-official
claude plugins install superpowers@claude-plugins-official
claude plugins install code-review@claude-plugins-official
claude plugins install playwright@claude-plugins-official
claude plugins install github@claude-plugins-official
claude plugins install stripe@claude-plugins-official
claude plugins install figma@claude-plugins-official
claude plugins install vercel@claude-plugins-official
claude plugins install firebase@claude-plugins-official
claude plugins install firecrawl@claude-plugins-official
```

Or as a one-liner:

```bash
for p in claude-md-management frontend-design linear plugin-dev ralph-loop security-guidance supabase superpowers code-review playwright github stripe figma vercel firebase firecrawl; do claude plugins install "$p@claude-plugins-official"; done
```

---

## Custom Skills

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

### `~/.claude/skills/excalidraw-diagrams/`

This is a full skill directory with Python scripts, tests, and a SKILL.md. **Copy the entire directory** from this machine:

```bash
# On old machine:
cp -r ~/.claude/skills/excalidraw-diagrams/ /path/to/transfer/

# On new machine:
cp -r /path/to/transfer/excalidraw-diagrams/ ~/.claude/skills/
```

The skill includes:
- `SKILL.md` — Full API reference for Diagram, Flowchart, AutoLayoutFlowchart, ArchitectureDiagram classes
- `scripts/excalidraw_generator.py` — Core generator library
- `scripts/layout_engine.py` — Auto-layout with grandalf
- `scripts/drive_helper.py` — Google Drive integration
- `scripts/export_playwright.js` — PNG export via headless browser
- `tests/` — Test suite

**Dependencies on new machine:**
```bash
pip install grandalf  # For AutoLayoutFlowchart
cd ~/.claude/skills/excalidraw-diagrams/scripts && npm install && npx playwright install chromium  # For PNG export
```

---

## Custom Commands (Global)

All go in `~/.claude/commands/`

### `/start` — `~/.claude/commands/start.md`

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

Then run a Linear health check (weekly -- skip if last check was <7 days ago per memory):
- Count total open issues across team BES. If approaching 200: warn about 250 free tier limit.
- Flag any issues that have been "In Progress" for >14 days -- they may be stuck or need re-scoping.
- If the same issues keep showing up in /start without being worked on, suggest triaging: close, deprioritize, or break them down.
- If any automation candidates from ~/.claude/projects/C--Users-danie/memory/project_linear_automations.md have hit their trigger threshold, flag them.
- Update memory with the check date when done.

Then check the weekly digest status:
- Look for the most recent twillot-bookmark*.csv in C:\Users\danie\Downloads\ (ignore .zip files)
- Read the latest file in C:\Users\danie\projects\x-bookmarks-kb\digests\
- If the CSV is older than 7 days: remind me to export fresh bookmarks from Twillot
- If the CSV is fresh but the last digest is older than 7 days: suggest running /digest
- If both are fresh and recent: skip this check silently

Keep it concise -- just enough to get oriented.
```

### `/wrap-up` — `~/.claude/commands/wrap-up.md`

```markdown
---
description: End-of-session documentation, tracker update, and Notion sync
---
Review what we accomplished in this session. Update the following files:

1. docs/changelog.md -- append a dated entry summarizing changes made.
   Write in a direct, analytical voice.

2. project-meta.json -- update:
   - last_updated to now
   - increment sessions_count
   - adjust status if project phase changed
   - add any new entries to skills_used, tools_used, plugins_used
   - update hours.ai_assisted_actual (estimate total hours spent with AI)
   - update hours.manual_estimate (estimate how long this would take without AI)
   - add a brief lesson_learned entry if applicable

3. docs/decisions.md -- log any architecture decisions made this session

4. docs/FORDAN.md -- if this was a significant session, update the learning
   document with what was built, how it works, and what was learned. Write
   engagingly, not like documentation.

5. CLAUDE.md -- update the Current Status section to reflect where we are.

6. Linear -- if we worked on a tracked issue this session:
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

Write all entries in a direct, analytical tone -- like a senior consultant
documenting outcomes for a stakeholder who needs to understand what happened
and why it matters.
```

### `/digest` — `~/.claude/commands/digest.md`

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
   > Open X.com -> Bookmarks -> Twillot extension -> Export CSV -> Save to Downloads"
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
6. **Push Weekly Picks to Notion (MANDATORY -- do not skip)**:
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
     - Part 1 (Claude Code & AI Coding) -> "AI Coding"
     - Part 2 (AI/ML Fundamentals) -> "AI/ML"
     - Part 3 (Product & Startups) -> "Product"
     - Part 4 (Finance & Investing) -> "Finance"
     - Part 5 (Personal Development) -> "Personal Dev"
   - **Dedup check**: Before creating each entry, search the database by Link URL. Skip if it already exists.
   - After pushing, report: "[X] new picks pushed to Notion, [Y] skipped (already existed)"
   - If Notion MCP is genuinely unavailable (tool error), warn Dan loudly -- do NOT silently skip
7. **Update the Weekly Picks Archive page (MANDATORY)**:
   - Fetch the Weekly Picks Archive page (`311c67a2483a81deb2a0cda1ae28d70a`)
   - Add a new section at the TOP of the page content (most recent first), below the intro text:
     - H2: "Week of [Monday date] - [Sunday date], YYYY"
     - H3: "Top Picks"
     - Numbered list of the picks pushed this week: each with title, source handle, key takeaway, and link
     - H3: "Putting Picks into Practice"
     - Placeholder text: *"Updated by `/weekly-recap`"*
   - This keeps the archive current automatically -- no manual editing needed
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

### `/weekly-recap` — `~/.claude/commands/weekly-recap.md`

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
- Also check for uncommitted changes (staged or unstaged) -- note active work-in-progress

Aggregate into a summary: "X commits across Y projects"

### Step 2: Check GitHub activity

- Run `gh api /users/dankenney/events --paginate -q '.[] | select(.type | test("Push|PullRequest|Create"))' | head -50`
- Filter to events from the past 7 days
- Capture: PRs opened/merged, repos pushed to, branches created
- Deduplicate with local git data from Step 1 -- note any GitHub-only activity

### Step 3: Summarize session insights

For each project with commits this week, read (if they exist):
- `docs/changelog.md` -- look for entries dated within the past 7 days
- `docs/FORDAN.md` -- check for recent learning notes
- `project-meta.json` -- read `lessons_learned` (latest entry), `skills_used`, `tools_used`
- `CLAUDE.md` -- read the "Current Status" section for what's in progress and what's next

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
- Also update the Weekly Picks Archive page (`311c67a2483a81deb2a0cda1ae28d70a`) -- find the corresponding week's "Putting Picks into Practice" sub-heading and replace placeholder text with the actual connections

### Step 4b: Sync project hours and status to Notion

For each project with commits this week:
- Read `project-meta.json` for `hours.ai_assisted_actual`, `hours.manual_estimate`, and `status`
- Calculate Hours Saved = `manual_estimate - ai_assisted_actual`
- Search the Project Lab database (data source: `collection://063f903c-6ec1-4431-92ea-66487e515b8b`) for the project by name
- Update each project entry:
  - `Hours Saved` = calculated value
  - `date:Last Updated:start` = today's date
  - `Status` = mapped from project-meta.json status:
    - "active" / "in-production" / "deployed" -> "In Production"
    - "building" / "in-development" -> "In Development"
    - "ideating" / "planning" -> "Ideating"
    - "paused" / "archived" -> "Paused"
- This keeps the Project Lab table current every time the recap runs

### Step 5: Sanitize the output

CRITICAL -- apply these rules to ALL output before pushing to Notion:
- Use `public_name` from each project's `project-meta.json` when available. NEVER use internal project folder names that contain company references.
- If no `public_name` exists, use a generic description (e.g., "competitive intelligence tool" instead of the company name)
- Strip any API keys, tokens, passwords, or credentials from commit messages
- Strip internal URLs (localhost, staging URLs with company names)
- No company names anywhere in the output -- per CLAUDE.md rules for Notion entries
- File paths are OK if they only reference project structure, not user-specific paths

### Step 6: Generate "Focus for Next Week"

- Read `CLAUDE.md` "Current Status" sections from projects with recent activity -- look for "Next" or "In progress" items
- Check Linear for top 3 open issues (In Progress or high priority, team BES):
  - Use MCP tools if available, or fall back to `linear` CLI
- Distill to 2-3 concrete, actionable focus items for the coming week
- Frame as outcomes ("Ship X", "Fix Y") not activities ("Work on X")

### Step 7: Format the recap

Use this exact format:

## Week of [Mon DD] - [Mon DD], YYYY

**Summary:** [X] commits across [Y] projects. [1-2 sentence narrative of the week's theme -- what was the main thrust of work?]

#### Project Highlights
**[Project public_name]** ([X] commits)
- [Key thing accomplished -- one bullet per notable change]

#### What I Learned
- [Insight from changelogs, FORDAN, or lessons_learned -- concrete and specific]

#### Picks Put into Practice
- [Pick title] -> Applied in [project public_name]: [how]
- (or "No direct connections this week -- but keeping an eye on [topic]")

#### Focus for Next Week
1. [Priority item -- framed as an outcome]
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
5. Replace all content between that heading and the "Weekly Picks" heading with the formatted recap
6. Update the Weekly Picks default view filter to show only the current week

### Step 9: Report

- Show the full recap content to Dan
- Confirm Notion push succeeded with the page URL: `https://dankenney.notion.site/Dan-s-Lab-30cc67a2483a8139abcbd1f6ee13a146`
- Flag any projects that had commits but no changelog or FORDAN data (gaps in documentation)
- If any projects lack a `public_name` in project-meta.json, warn and suggest adding one

## Rules

- Write in a direct, analytical voice -- like a senior engineer's weekly standup
- Avoid AI slop phrases (see banned list in CLAUDE.md)
- Keep project highlights to 1-2 sentences each -- scannable, not exhaustive
- The recap should take ~30 seconds to read
- If no activity found in the past 7 days, say so clearly and skip to "Focus for Next Week"
```

### `/sync-patterns` — `~/.claude/commands/sync-patterns.md`

```markdown
---
description: Sync new Agentic Engineering Patterns from Simon Willison's guide series
---

## What This Does
Checks Simon Willison's Agentic Engineering Patterns guide for new chapters and tagged posts, distills actionable rules, and proposes updates to Dan's rules file.

## Step 1: Check for New Content

1. Read the current rules file at `~/.claude/rules/agentic-patterns.md`
   - Note the "Chapters processed" and "Tags reviewed" lines -- these are the state tracker
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
   - YES -> draft 1-3 concise rules in imperative mood
   - NO (purely philosophical) -> acknowledge it, note it in the header, skip rules
3. Show Dan the proposed additions before writing anything

## Step 3: Review New Tagged Posts

For each new tagged post:
1. Fetch and read the content
2. Most tagged posts are commentary or links -- they rarely yield rules
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

- NEVER auto-write to the rules file -- always show Dan first
- Each rule must be independently understandable without reading the source
- Prefer 1 concrete rule over 3 vague ones
- If a chapter is purely philosophical, it's OK to extract zero rules
- Keep the file under 60 lines total
- Match the existing style: `### Section Header` then `- Rule in imperative mood`
```

### `/commit-push-pr` — `~/.claude/commands/commit-push-pr.md`

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

### `/fix-issue` — `~/.claude/commands/fix-issue.md`

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

### `/refactor` — `~/.claude/commands/refactor.md`

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

### `/review` — `~/.claude/commands/review.md`

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

---

## Custom Commands (Project-Level)

These live in `cc-foundation-guide/.claude/commands/` and are specific to this project.

### `/status` (project) — `.claude/commands/status.md`

```markdown
---
description: Quick project status summary
---
Read project-meta.json and CLAUDE.md. Provide a brief status report:
- Project name and current status
- Last updated date and session count
- Hours saved estimate (manual vs AI-assisted)
- What's in progress and what's next
- Any blockers or decisions needed
```

### `/push-to-notion` (project) — `.claude/commands/push-to-notion.md`

```markdown
---
description: Sync current project status from project-meta.json to Notion Project Lab
---
Sync this project's current state to Notion Project Lab.

1. Read:
   - `project-meta.json`
   - `docs/notion-hub-config.md`

2. Validate prerequisites:
   - Resolve `Project Lab` target ID from `docs/notion-hub-config.md`:
     - Prefer the `Data source: ...` ID in the `Notes` column when present.
     - Otherwise use the `Project Lab` value in the `Notion ID` column.
   - Confirm a usable target ID exists.
   - Confirm Notion MCP access is available.
   - If either check fails, stop and report exactly what is missing.

3. Build payload from `project-meta.json`:
   - `Name` = `public_name` if present, otherwise `name`
   - `Status` mapping:
     - `in-production` -> `In Production`
     - `in-development` -> `In Development`
     - `ideating` -> `Ideating`
     - `paused` -> `Paused`
   - `Description` = `description`
   - `Hours Saved` = `hours.manual_estimate - hours.ai_assisted_actual`
   - `Skills Used` = merged unique values from `skills_used` and `tools_used`
   - `Lessons Learned` = latest entry in `lessons_learned`
   - `GitHub URL` = `github_url`
   - `Live URL` = `live_url`
   - `Last Updated` = `last_updated`

4. Upsert in Project Lab:
   - Search for an existing page where `Name` matches `public_name` (or `name` if no public_name).
   - Update if found; create if not found.
   - If `public_name` is missing from project-meta.json, warn the user and suggest adding one.

5. Return:
   - `updated` or `created`
   - Notion page URL
   - Any field-level warnings
```

### `/setup-notion-hub` (project) — `.claude/commands/setup-notion-hub.md`

```markdown
---
description: Set up Notion hub databases and capture IDs/URLs for this project
---
Set up the Notion hub for this project in the exact sequence below.

1. Verify MCP connectivity:
   - Search my Notion workspace for any page.
   - If OAuth authorization is required, pause and ask me to complete auth in the browser, then continue.

2. Create these databases in Notion:
   - **Project Lab** with properties:
     - Name (title)
     - Status (select: In Production, In Development, Ideating, Paused)
     - Description (text)
     - Hours Saved (number)
     - Skills Used (multi-select)
     - Lessons Learned (text)
     - GitHub URL (url)
     - Live URL (url)
     - Last Updated (date)
   - **Resources** with properties:
     - Title (title)
     - Category (select: Books, Podcasts, Substacks, Tools, Courses, Articles)
     - Creator (text)
     - URL (url)
     - My Take (text)
     - Tags (multi-select)
   - **Skills Catalog** with properties:
     - Name (title)
     - Type (select: Skill, Plugin, MCP, Hook, Command)
     - Install Command (text)
     - Source (url)
     - Notes (text)

3. Create a landing page titled **Dan's Lab** and embed all three databases with clean default views.

4. Seed **Project Lab** with one entry from this file:
   - `project-meta.json`

5. Update `docs/notion-hub-config.md` with:
   - Workspace name
   - Notion IDs for all three databases and the landing page
   - Internal URLs
   - Public URLs (if published)
   - Status values in the setup checklist

6. Return a short completion report:
   - What was created
   - Which checklist items are complete vs pending
   - Any blockers or follow-up actions needed
```

### `/wrap-up` (project) — `.claude/commands/wrap-up.md`

```markdown
---
description: End-of-session documentation, tracker update, and Notion sync
---
Review what we accomplished in this session and execute the workflow in order.

## 1) Update local project files

1. `docs/changelog.md`
   - Append a dated entry summarizing concrete changes made this session.
   - Use a direct, analytical tone.

2. `project-meta.json`
   - Set `last_updated` to now (ISO date).
   - Increment `hours.sessions_count` by 1.
   - Adjust `status` if the phase changed.
   - Add any new entries to `skills_used`, `tools_used`, and `plugins_used`.
   - Update `hours.ai_assisted_actual` (cumulative estimate).
   - Update `hours.manual_estimate` (cumulative non-AI estimate).
   - Append one short `lessons_learned` entry if there is a meaningful takeaway.
   - If `public_name` is missing, add one. The public_name should be a generic, public-facing name that avoids company-specific references.

3. `docs/decisions.md`
   - Add any architecture/process decisions made this session.

4. `docs/FORDAN.md`
   - If the session was significant, update the learning narrative with what was built, how it works, and lessons learned.

5. `CLAUDE.md`
   - Update `Current Status` (`In progress`, `Next`, and `Completed` when relevant).

## 2) Push status to Notion Project Lab

After local files are updated, sync to Notion using MCP.

1. Read both files:
   - `project-meta.json`
   - `docs/notion-hub-config.md`

2. Validate prerequisites:
   - Resolve `Project Lab` target ID from `docs/notion-hub-config.md`:
     - Prefer the `Data source: ...` ID in the `Notes` column when present.
     - Otherwise use the `Project Lab` value in the `Notion ID` column.
   - If no usable target ID is found, stop Notion sync and report why.
   - If MCP access is unavailable, stop Notion sync and report why.

3. Build sync payload from `project-meta.json`:
   - `Name` = `public_name` if present, otherwise `name`
   - `Status` mapping:
     - `in-production` -> `In Production`
     - `in-development` -> `In Development`
     - `ideating` -> `Ideating`
     - `paused` -> `Paused`
     - otherwise: best-fit title case value
   - `Description` = `description`
   - `Hours Saved` = `hours.manual_estimate - hours.ai_assisted_actual`
   - `Skills Used` = merged unique values from `skills_used` and `tools_used`
   - `Lessons Learned` = most recent entry from `lessons_learned`
   - `GitHub URL` = `github_url`
   - `Live URL` = `live_url`
   - `Last Updated` = `last_updated`

4. Upsert behavior in Project Lab:
   - Search for an existing page where `Name` matches the project `name`.
   - If found, update that record with the payload.
   - If not found, create a new record with the payload.

5. Report sync result:
   - Indicate whether a record was updated or created.
   - Return the Notion page URL.
   - Note any fields that could not be set (for example, schema mismatch).

## 3) Final response format

End with a short wrap-up summary:
- Local files updated
- Notion sync status (`updated`, `created`, or `skipped`)
- Follow-up actions (if any blockers remain)

Write all entries in a direct, stakeholder-ready tone.
```

---

## Notion Database IDs (Reference)

These are needed by the commands above:

| Database | Data Source ID | Purpose |
|----------|---------------|---------|
| Project Lab | `063f903c-6ec1-4431-92ea-66487e515b8b` | Project tracking |
| Weekly Picks | `1936fbd5-5e37-484b-82d3-4d518bd695ae` | Bookmark highlights |
| Skills Catalog | `65821b67-54d2-4683-af6f-0ef7643d92fb` | Installed tools/skills |
| Dan's Lab (page) | `30cc67a2-483a-8139-abcb-d1f6ee13a146` | Landing page |
| Weekly Picks Archive (page) | `311c67a2483a81deb2a0cda1ae28d70a` | Historical picks |

---

## X Bookmarks Knowledge Base

The `x-bookmarks-kb` repo is a separate Git repository. Clone it on the new machine:

```bash
cd ~/projects
git clone https://github.com/dankenney/x-bookmarks-kb.git
```

It contains:
- `README.md` — The full knowledge base (~2,300 lines, 779+ bookmarks organized into 5 parts)
- `digests/` — Weekly digest files (5 so far: 2026-02-20 through 2026-03-13)

The `/digest` command (above) processes new Twillot CSV exports against this repo.

---

## System Dependencies

Install these on the new machine:

```bash
# Core
# Git Bash (bundled with Git for Windows)
# Node.js (LTS)
# Python 3.x

# Formatters (used by hooks)
npm install -g prettier
pip install black

# GitHub CLI
# Download from https://cli.github.com/
gh auth login

# Excalidraw skill dependencies
pip install grandalf
cd ~/.claude/skills/excalidraw-diagrams/scripts && npm install && npx playwright install chromium
```

---

## File Tree Summary

```
~/.claude/
  CLAUDE.md                          # Global config
  settings.json                      # Hooks, MCP servers, permissions, plugins
  rules/
    agentic-patterns.md              # Willison's patterns (auto-synced)
    git-workflow.md                  # Commit standards
    security.md                      # Security rules
    testing.md                       # Testing rules
  skills/
    code-review.md                   # Code review checklist
    debug-workflow.md                # Debugging skill
    excalidraw-diagrams/             # Full diagram generator (Python + JS)
      SKILL.md
      scripts/
      tests/
  commands/
    start.md                         # /start — session opener
    wrap-up.md                       # /wrap-up — session closer
    digest.md                        # /digest — bookmark processing
    weekly-recap.md                  # /weekly-recap — activity summary
    sync-patterns.md                 # /sync-patterns — Willison sync
    commit-push-pr.md                # /commit-push-pr — git workflow
    fix-issue.md                     # /fix-issue — GitHub issue fixer
    refactor.md                      # /refactor — safe refactoring
    review.md                        # /review — code review
  plugins/
    installed_plugins.json           # 16 plugins (reinstall via commands above)

~/projects/
  cc-foundation-guide/               # This guide + Foundation Package
    .claude/commands/
      status.md                      # /status (project)
      wrap-up.md                     # /wrap-up (project, with Notion sync)
      push-to-notion.md              # /push-to-notion
      setup-notion-hub.md            # /setup-notion-hub
  x-bookmarks-kb/                    # Bookmark knowledge base
    README.md                        # Full KB (~779 bookmarks)
    digests/                         # Weekly digests
```
