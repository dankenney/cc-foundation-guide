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
