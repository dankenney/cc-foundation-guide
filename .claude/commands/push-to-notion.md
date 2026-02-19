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
   - `Name` = `public_name` if present, otherwise `name` (public_name is the anonymized, public-facing name that strips company-specific references)
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
