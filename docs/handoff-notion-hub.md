# Handoff: Notion Hub Setup

**Date:** 2026-02-19
**Status:** Complete

---

## What's Done

### Infrastructure
1. **Init script** (`~/scripts/init-project.sh`) — Working. Creates full Foundation Package for new projects.
2. **Project scanner** (`~/scripts/scan-projects.sh`) — Working. Scans `~/projects/*/project-meta.json` and prints health summary. All 9 projects visible.

### Notion Hub (Complete)
3. **Dan's Lab landing page** — Created and published at `https://dankenney.notion.site/Dan-s-Lab-30cc67a2483a8139abcbd1f6ee13a146`
4. **Project Lab** database — 9 projects with anonymized public names, status, hours saved, skills used, lessons learned. Data source: `063f903c-6ec1-4431-92ea-66487e515b8b`
5. **Resources** database — 7 entries (Stulberg, Cherny, Anthropic docs, Everything CC, HumanLayer, Karpathy skills, Shankar). Data source: `3677debd-77b7-4949-9277-caed00f9517f`
6. **Skills Catalog** database — 11 entries (commands, skills, MCPs, hooks). Data source: `65821b67-54d2-4683-af6f-0ef7643d92fb`
7. **Getting Started Guides** database — 12 entries (templates, commands, config, guides, references). Data source: `a289eafb-47b8-458f-9c9d-ffa62db0e5be`

### Automation
8. **`/push-to-notion`** command — Syncs project-meta.json to Notion. Uses `public_name` for anonymized display. Upserts by name.
9. **`/wrap-up`** command — Updated to sync to Notion after local file updates. Auto-prompts for `public_name` if missing.
10. **`public_name`** field — Added to all 8 project-meta.json files. Maps internal names (e.g., "Codex YEB") to public-facing names (e.g., "EY Competitor Intelligence Dashboard").

### All IDs and URLs
See `docs/notion-hub-config.md` for the complete registry of Notion IDs, data source IDs, and URLs.

## What's Next

### Remaining View-Level Tweaks (Notion UI, manual)
- Drag Status and Skills Used columns next to Name in Project Lab
- Enable text wrapping on Description column
- These are view settings the MCP can't control

### Future Enhancements
- **Simple personal site** — Build the static site linking to Dan's Lab (see website-notion-guide.md Section 3)
- **Bulk sync script** — A script that reads all `~/projects/*/project-meta.json` and pushes all to Notion in one pass
- **GitHub URLs** — Update as repos are pushed to GitHub

## Key Files

| File | Path | Purpose |
|------|------|---------|
| Notion hub config | `docs/notion-hub-config.md` | Source of truth for all Notion IDs and URLs |
| Push-to-Notion command | `.claude/commands/push-to-notion.md` | Sync project to Notion |
| Wrap-up command | `.claude/commands/wrap-up.md` | End-of-session with Notion sync |
| Setup command | `.claude/commands/setup-notion-hub.md` | One-time Notion hub creation |
| Init script | `~/scripts/init-project.sh` | New project scaffolding |
| Project scanner | `~/scripts/scan-projects.sh` | Cross-project health check |
| Foundation guide | `cc-foundation-guide.md` | Master reference document |
| Website/Notion guide | `website-notion-guide.md` | Site + Notion architecture |

## Project Name Mapping

| Internal Name | Public Name (Notion) |
|---|---|
| cc-foundation-guide | AI Coding Agent Foundation Guide |
| YPE | Competitive Pricing Monitor |
| Codex YEB | EY Competitor Intelligence Dashboard |
| Codex-Cat-15 | Financed Emissions Calculator (PCAF) |
| Codex FMLA | SEC Absence Intelligence Pipeline |
| IPO | IPO ESG Mining Pipeline |
| x-bookmarks-kb | X Bookmarks Knowledge Base |
| Approve bot | Claude Code Auto-Approver |
| roi-tool | Sustainability ROI Calculator |
