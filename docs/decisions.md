# Architecture Decisions

## Template

### [Date] - [Decision Title]
**Context:** What situation prompted this decision?
**Decision:** What did we decide?
**Alternatives considered:** What else did we look at?
**Rationale:** Why this choice?

---

### 2026-02-19 - Project Initialized
**Context:** Starting a new project.
**Decision:** Use the Foundation Package structure from cc-foundation-guide.
**Rationale:** Consistent project structure enables better CC context and cross-project tracking.

---

### 2026-02-19 - Keep Notion IDs in Versioned Project Docs
**Context:** Step 2 introduced external Notion resources (three databases and one landing page) that Step 3 automation depends on.
**Decision:** Store Notion IDs and URLs in `docs/notion-hub-config.md` and keep it updated as the canonical registry.
**Alternatives considered:** Keeping IDs only in chat context, or embedding them only in `CLAUDE.md`.
**Rationale:** A dedicated tracked file reduces context loss and makes `/wrap-up` integration deterministic across sessions and tools.

---

### 2026-03-03 - Standalone Getting-Started Guide Over Guide Subset

**Context:** A friend needed Claude Code onboarding. The full foundation guide (cc-foundation-guide.md) covers everything but is too dense for someone who just got access and wants to be productive fast.
**Decision:** Create a standalone `getting-started.md` with progressive stages (First Hour, First Week, First Month, Going Further) rather than extracting sections from the main guide or creating a "lite" version.
**Alternatives considered:** (1) Sharing sections 0, 2, and 8 of the main guide directly. (2) Creating a condensed version of the main guide. (3) Writing a one-pager cheat sheet.
**Rationale:** A standalone doc with its own narrative arc serves beginners better than excerpts that lose context. The progressive structure respects that learning Claude Code is iterative — you don't need to understand skills and hooks on day one. Cross-references to the full guide let readers go deeper when ready.

---

### 2026-02-19 - Treat Public Database URLs as Optional for Step 2 Completion
**Context:** Notion publish controls are page-level and direct public URLs for each database are not required for local-to-Notion sync.
**Decision:** Mark Step 2 complete once OAuth, database creation, landing page creation, and internal URL capture are done; keep per-database public URLs as a follow-up task.
**Alternatives considered:** Blocking Step 3 until every database had a direct public URL.
**Rationale:** Step 3 integration uses internal IDs/URLs, so waiting for public URL polish would slow execution without reducing core integration risk.
