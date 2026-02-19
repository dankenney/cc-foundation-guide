# Notion Hub Config

This file is the source of truth for Notion IDs and URLs used by this project.

## Setup Status
- Date started: 2026-02-19
- MCP endpoint configured in `~/.claude/settings.json`: yes (`https://mcp.notion.com/mcp`)
- OAuth authorized in Claude Code: complete
- Databases created: complete
- Landing page created: complete

## Workspace
- Workspace name: Dan's personal workspace (teamspace: General)
- Landing page title: Dan's Lab

## Resource Registry
| Resource | Notion ID | Internal URL | Public URL | Status | Notes |
| --- | --- | --- | --- | --- | --- |
| Project Lab | c15c5d12-00d0-4cab-95c1-f2c877c7bd40 | https://www.notion.so/c15c5d1200d04cab95c1f2c877c7bd40 | pending publish | created | Data source: 063f903c-6ec1-4431-92ea-66487e515b8b |
| Resources | f2ebfaa7-2c5d-4e30-bc14-0b06ccca1f9a | https://www.notion.so/f2ebfaa72c5d4e30bc140b06ccca1f9a | pending publish | created | Data source: 3677debd-77b7-4949-9277-caed00f9517f |
| Skills Catalog | b0e71b40-3b5c-4559-ac99-fcd8d55418a0 | https://www.notion.so/b0e71b403b5c4559ac99fcd8d55418a0 | pending publish | created | Data source: 65821b67-54d2-4683-af6f-0ef7643d92fb |
| Dan's Lab Landing Page | 30cc67a2-483a-8139-abcb-d1f6ee13a146 | https://www.notion.so/30cc67a2483a8139abcbd1f6ee13a146 | https://dankenney.notion.site/Dan-s-Lab-30cc67a2483a8139abcbd1f6ee13a146 | created | Parent page for all databases |

## Project Lab Schema
- Name (title)
- Status (select: In Production, In Development, Ideating, Paused)
- Description (text)
- Hours Saved (number)
- Skills Used (multi-select: claude-code, git-bash)
- Lessons Learned (text)
- GitHub URL (url)
- Live URL (url)
- Last Updated (date)

## Verification Checklist
- [x] Notion search via MCP returns results
- [x] All 3 databases are created with required fields
- [x] Landing page is created and embeds all database views
- [x] Project Lab is seeded with `cc-foundation-guide` from `project-meta.json`
- [x] Internal Notion URLs are captured above
- [ ] Public URLs are captured above after Share -> Publish
