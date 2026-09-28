---
id: proc-new-project-init
type: procedural
title: New Project Initialisation Loop
created: 2026-09-15
updated: 2026-09-15
author: system
confidence: high
ttl: permanent
status: active
tags: [init, new-project, setup, topic-map]
related: [proc-session-loop, proc-memory-assembly, proc-lint-pass]
source: system
---

# Workflow: New Project Initialisation Loop

## Purpose

Run this workflow once when setting up a brand new vault for any project. It seeds the vault with the right topic-chunked memory structure before any sessions begin. After this init is complete, switch to `procedural/session-loop.md` for all future sessions.

## When To Use This

- Starting any new project vault from the template
- Onboarding a new client, user, or domain into the system
- Setting up one of the 5 core use cases for the first time

## The 5 Core Use Cases This Supports

| Use Case | What Gets Seeded |
|---|---|
| **Personal AI Assistant** | User profile, preferences, life context, communication style |
| **Software Development** | Tech stack, architecture decisions, team, repo structure |
| **Research & Knowledge** | Hypotheses, key sources, methodology, open questions |
| **Client & Business** | Client profile, history, contracts, contacts, preferences |
| **Content Creation** | Voice/style guide, topics covered, audience, content calendar |

---

## The 5-Prompt Init Loop

### Init Prompt 1 — Context Dump & Topic Map Request

Copy, fill in the brackets, and send:

> "This is a new [project type — e.g. software project / personal assistant / research project]. Here is all the relevant context: [paste everything you want the system to know — background, goals, key facts, people involved, constraints, preferences]. Analyse this context and propose a topic map for the semantic memory files. List each proposed filename and a one-line description of what it will contain. Do not create any files yet. Wait for my approval."

**What the AI does:**
- Reads and analyses all provided context
- Identifies natural topic clusters based on the project type
- Proposes a topic map: a list of `semantic/filename.md → what it contains`
- Waits — writes nothing

**What you do:**
- Review the proposed topic map
- Add topics that are missing
- Remove topics that aren't needed
- Rename any topics that don't feel right
- Move to Init Prompt 2

---

### Init Prompt 2 — Topic Map Approval

Send your edited topic map back, or send:

> "Topic map approved as proposed." or "Here is the revised topic map: [paste your edits]."

**What the AI does:**
- Confirms the final approved topic map
- Prepares to generate files based on it

---

### Init Prompt 3 — Generate Semantic Memory Files

Send:

> "Generate the initial semantic memory files based on the approved topic map. Write them all to _inbox/ with status: pending. Use the topic-chunking rules — group related facts together, use the correct frontmatter schema, and fill in as much as you can from the context I provided. Flag any fields where you need more information from me."

**What the AI does:**
- Creates one file per approved topic
- Populates each file with facts extracted from the provided context
- Flags gaps where information wasn't provided
- Writes all files to `_inbox/` — nothing goes to active folders yet

---

### Init Prompt 4 — Review Generated Files

Send:

> "Show me each generated file one by one. Present the full content of each file for my review. I'll approve, edit, or reject each one before anything is committed."

**What the AI does:**
- Presents each `_inbox/` file in full, one at a time
- Waits for your response on each before showing the next
- Notes any flags or gaps it identified

**What you do:**
- Read each file carefully — this is the foundation of your vault
- Edit directly in the conversation if something is wrong or missing
- Mark each as approved, edited, or rejected
- When all files are reviewed, move to Init Prompt 5

---

### Init Prompt 5 — Commit & Confirm Vault Ready

Send:

> "All reviewed. Commit the approved files — run the lint pass to promote them from _inbox/ to their target folders, update _meta/index.md with the project topic map and vault stats, register this project in _meta/models.md, and confirm the vault is ready for regular sessions."

**What the AI does:**
- Runs lint pass on `_inbox/`
- Promotes all approved files to `semantic/`
- Updates `_meta/index.md` with the full topic map and initial vault stats
- Logs `SESSION_END` and init completion in `_meta/log.md`
- Outputs a confirmation: vault is ready, files created, topic map recorded

**What you do:**
- Read the confirmation
- Save the vault
- From next session onwards, use `procedural/session-loop.md`

---

## Topic Map Examples By Use Case

### Personal AI Assistant
```
semantic/user-profile.md          → identity, background, personal context
semantic/user-preferences.md      → communication style, tools, habits
semantic/ongoing-projects.md      → active personal projects and goals
semantic/relationships.md         → key people, roles, context
```

### Software Development Project
```
semantic/project-overview.md      → goals, scope, timeline, stakeholders
semantic/tech-stack.md            → languages, frameworks, infrastructure decisions
semantic/architecture.md          → system design decisions and rationale
semantic/team.md                  → team members, roles, responsibilities
semantic/conventions.md           → coding standards, naming rules, workflow norms
```

### Research Project
```
semantic/research-overview.md     → topic, goals, methodology, timeline
semantic/hypotheses.md            → active hypotheses and their status
semantic/key-sources.md           → primary sources, authors, institutions
semantic/findings.md              → confirmed findings to date
semantic/open-questions.md        → unresolved questions driving next steps
```

### Client & Business Management
```
semantic/client-profile.md        → company background, industry, size, contacts
semantic/relationship-history.md  → how the relationship started, key moments
semantic/active-engagements.md    → current projects, contracts, deliverables
semantic/preferences.md           → communication style, decision makers, sensitivities
semantic/commercial.md            → pricing, terms, renewal dates
```

### Content Creation
```
semantic/voice-and-style.md       → tone, style rules, vocabulary preferences
semantic/audience.md              → who the content is for, what they care about
semantic/topics-covered.md        → content already published, themes explored
semantic/content-calendar.md      → upcoming content, deadlines, platforms
semantic/brand-guidelines.md      → visual and verbal brand rules
```

---

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-09-15 | system | Initial release |
