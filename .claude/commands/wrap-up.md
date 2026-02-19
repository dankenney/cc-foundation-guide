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
   - If `public_name` is missing, add one. The public_name should be a generic, public-facing name that avoids company-specific references (e.g., "Codex YEB" becomes "EY Competitor Intelligence Dashboard").

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
