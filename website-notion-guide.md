# Personal Website + Notion Hub Guide

### A Two-Layer Approach to Tracking and Showcasing Your Work

> **Last updated:** 2026-02-15  
> **Companion to:** [Claude Code Foundation Guide](./cc-foundation-guide.md)  
> **Purpose:** Set up a lightweight personal website linked to a Notion hub that tracks projects, resources, and lessons learned — with minimal ongoing maintenance.

---

## Table of Contents

1. [Why Two Layers Instead of One](#1-why-two-layers)
2. [The Architecture](#2-the-architecture)
3. [Layer 1: The Simple Site](#3-layer-1-the-simple-site)
4. [Layer 2: Notion Hub](#4-layer-2-notion-hub)
5. [Wiring It Together: /wrap-up → Notion](#5-wiring-it-together)
6. [Build Order & CC Prompts](#6-build-order)

---

## 1. Why Two Layers

A custom website with filterable project cards and recommendation databases is fun to build but tedious to maintain. You'd need to update JSON files, push to GitHub, wait for deployment. The maintenance cost kills the project within weeks.

Instead, split the problem:

**Layer 1: Simple personal site** — Your public face. Name, one-liner, links, a few featured things. Think Claire Vo's "Hey y'all" site. Takes one CC session to build. Almost never needs updating. This is your business card, not your portfolio.

**Layer 2: Notion as the resource/tracking hub** — Where the meaty stuff lives. Project tracker, skills catalog, lessons learned, book recommendations, podcast notes, bookmarked resources. Notion gives you everything without building anything:

- **Mobile-native** — update from your phone while waiting for coffee
- **Shareable via browser link** — anyone can view, no login required
- **Searchable** — find anything across all your databases
- **CC can sync to it** — bidirectional via Notion MCP (`/push-to-notion`, `/pull-from-notion`)
- **Free to start** — Notion's free tier is more than enough

---

## 2. The Architecture

```
CC session → /wrap-up updates project-meta.json locally
           → also pushes status update to Notion project tracker via MCP

Simple website links to → Notion "Project Lab" (public page)
                        → Notion "Resources" (public page)
                        → Notion "Lessons Learned" (public page)  
                        → GitHub profile
                        → LinkedIn, Substack, etc.
```

The key insight: the simple site is static and almost never changes. The Notion pages are dynamic and update automatically through your CC workflow.

---

## 3. Layer 1: The Simple Site

**Tech:** Single HTML page or minimal framework. Hosted on Vercel or Netlify.

**Content:**

- Your name and a positioning tagline
- What you're working on (2-3 sentences)
- Links to Notion pages, GitHub, LinkedIn, and other profiles
- Optional: 3-4 "featured project" cards that you update quarterly

### CC Prompt to Build It

```
Create a minimal, elegant personal website — a single page. 

Content:
- Name: [your name]
- Tagline: [your positioning — what you do and for whom]
- A brief "What I'm Working On" section (3-4 sentences, placeholder text)
- Links section pointing to:
  - Project Lab (Notion link — placeholder URL)
  - Resources & Recommendations (Notion link — placeholder URL)  
  - GitHub profile
  - LinkedIn profile
  - X/Twitter profile

Design:
- Dark mode default, clean and minimal
- No emojis, no gradients, no generic tech aesthetic
- Inter or similar clean sans-serif font
- Think: "professional who builds things" — polished but not corporate
- Responsive, looks great on mobile
- Very fast — minimal JavaScript, no framework bloat

Tech: Single HTML file with inline CSS. No build step needed.
Host on Vercel or Netlify via GitHub.

Create this at ~/projects/personal-site/ with the full foundation package
(CLAUDE.md, project-meta.json, etc.)
```

### Deployment

1. Push to a GitHub repo
2. Connect to Vercel or Netlify (free tier)
3. Point your custom domain if you have one
4. Done — auto-deploys on push, but you'll rarely need to push

---

## 4. Layer 2: Notion Hub

Set up a Notion workspace with these public databases:

### Database 1: Project Lab

| Property        | Type         | Purpose                                         |
| --------------- | ------------ | ----------------------------------------------- |
| Name            | Title        | Project name                                    |
| Status          | Select       | In Production, In Development, Ideating, Paused |
| Description     | Text         | Brief project description                       |
| Hours Saved     | Number       | Manual estimate minus AI-assisted actual        |
| Skills Used     | Multi-select | Tags for skills/tools used                      |
| Lessons Learned | Text         | Key takeaways                                   |
| GitHub URL      | URL          | Link to repo                                    |
| Live URL        | URL          | Link to deployed version                        |
| Last Updated    | Date         | When CC last pushed an update                   |

### Database 2: Resources & Recommendations

| Property | Type         | Purpose                                              |
| -------- | ------------ | ---------------------------------------------------- |
| Title    | Title        | Resource name                                        |
| Category | Select       | Books, Podcasts, Substacks, Tools, Courses, Articles |
| Creator  | Text         | Author/host/creator                                  |
| URL      | URL          | Link                                                 |
| My Take  | Text         | Your one-line assessment                             |
| Tags     | Multi-select | Topics covered                                       |

### Database 3: Skills & Plugins Catalog

| Property         | Type     | Purpose                           |
| ---------------- | -------- | --------------------------------- |
| Name             | Title    | Skill or plugin name              |
| Type             | Select   | Skill, Plugin, MCP, Hook, Command |
| Install Command  | Text     | How to install it                 |
| Source           | URL      | GitHub repo or docs link          |
| Projects Used In | Relation | Links to Project Lab entries      |
| Notes            | Text     | Usage tips and gotchas            |

### CC Prompt to Set Up Notion (requires Notion MCP configured)

```
I have the Notion MCP connected. Help me set up my personal hub:

1. Create a "Project Lab" database with these properties: Name (title), 
   Status (select: In Production, In Development, Ideating, Paused), 
   Description (text), Hours Saved (number), Skills Used (multi-select), 
   Lessons Learned (text), GitHub URL (url), Live URL (url), 
   Last Updated (date)

2. Create a "Resources" database with: Title (title), 
   Category (select: Books, Podcasts, Substacks, Tools, Courses, Articles), 
   Creator (text), URL (url), My Take (text), Tags (multi-select)

3. Create a "Skills Catalog" database with: Name (title), 
   Type (select: Skill, Plugin, MCP, Hook, Command), 
   Install Command (text), Source (url), Notes (text)

4. Create a landing page called "[Your Name]'s Lab" that embeds all three 
   databases with filtered views. Make it look clean.

5. Update my CLAUDE.md with the Notion database IDs and URLs so 
   future sessions can push to them automatically.
```

### Making Notion Pages Public

Once your databases are populated:

1. Open the landing page in Notion
2. Click "Share" → "Publish" → toggle "Share to web"
3. Copy the public URL
4. Paste it into your simple site's links section

---

## 5. Wiring It Together

### Updating the /wrap-up Command for Notion

Once your Notion MCP is configured, extend the `/wrap-up` command (in `.claude/commands/wrap-up.md`) to push updates after the local file updates:

```
After updating the local files (changelog, project-meta.json, etc.), 
also push to Notion:

1. Search for this project in the Project Lab database
2. If found, update: Status, Hours Saved, Skills Used, Lessons Learned, 
   Last Updated
3. If not found, create a new entry with all fields populated from 
   project-meta.json
4. Show me the Notion URL when done
```

### Additional Notion Prompts

**Push to Notion (standalone):**

```
Push this project's current status to my Notion Project Lab database. 
Search for the project first — update if it exists, create if it doesn't. 
Include: status, hours saved, skills used, latest lesson learned, 
and last updated date.
```

**Monthly Portfolio Review:**

```
Run ~/scripts/scan-projects.sh and show me the output. Then review my 
Notion Project Lab for anything that looks stale (not updated in 30+ days). 
For stale projects, suggest whether to mark them as paused or if there's 
an obvious next step. Also check if any lessons_learned entries would be 
useful to add to the foundation guide.
```

**Add a Recommendation:**

```
Add this to my Notion Resources database:
- Title: [name]
- Category: [books/podcasts/substacks/tools/courses/articles]
- Creator: [author/host]
- URL: [link]
- My Take: [one-line assessment]
- Tags: [relevant topics]
```

---

## 6. Build Order

When you're ready to set this up, tackle in this order:

| Step | What                                         | Time   | Depends On               |
| ---- | -------------------------------------------- | ------ | ------------------------ |
| 1    | Init script (from Foundation Guide)          | 30 min | Nothing                  |
| 2    | Notion hub — create databases + landing page | 30 min | Notion MCP configured    |
| 3    | Update /wrap-up for Notion                   | 15 min | Steps 1 + 2              |
| 4    | Simple personal site                         | 30 min | Step 2 (for Notion URLs) |
| 5    | First real project using the full pipeline   | —      | Steps 1-4                |

Total setup time: ~2 hours across 4 CC sessions.

After setup, the pipeline runs automatically:

```
Work with CC → /wrap-up → Local files + Notion updated automatically
Simple site links to Notion → Always current
```

Zero ongoing maintenance beyond running `/wrap-up` at the end of each session (which you should be doing anyway for the foundation guide workflow).

---

*See the [Claude Code Foundation Guide](./cc-foundation-guide.md) for the core development workflow this integrates with.*
