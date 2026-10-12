---
id: proc-memory-assembly
type: procedural
title: Working Memory Assembly Workflow — V2
created: 2026-10-08
updated: 2026-10-08
author: system
confidence: high
ttl: permanent
status: active
tags: [working-memory, session, assembly, relevance-scoring, v2]
related: [proc-lint-pass, proc-inbox-promotion, proc-session-loop]
source: system
---

# Workflow: Working Memory Assembly — V2

## Purpose

Defines how an AI model assembles its working memory at session start. V2 introduces relevance scoring to ensure only the most pertinent files are loaded — preventing context bloat as the vault grows over time.

## Trigger Condition

- Start of every new session

---

## Steps

### Step 1 — Always Load First (mandatory)
- `_meta/CLAUDE.md` — rules and schema
- `_meta/index.md` — vault catalog and health summary
These two files load unconditionally on every session.

### Step 2 — Identify Session Topic
Before loading any memory files, identify the session topic from:
- The user's opening prompt
- The project type recorded in index.md
- The most recent episodic file title
This topic drives all relevance scoring in Steps 3-6.

### Step 3 — Score And Load Semantic Files

**Relevance scoring — apply to every active semantic file:**
- +3 points: each tag matching the session topic
- +2 points: `updated:` date within last 7 days
- +2 points: file is in `related:` field of another file already selected
- +1 point: `created:` date within last 30 days
- +1 point: filename contains session topic keyword

**Load only files scoring 3 or above.**
**Hard limit: maximum 7 semantic files per session.**

If no files score 3 or above, load the 3 most recently updated semantic files as a fallback.

### Step 4 — Load Episodic Files
- Load the 3 most recent episodic files by `created:` date
- Prioritise episodes tagged with the current session topic
- Skip episodes older than 90 days (should be compressed already)
- Hard limit: maximum 3 episodic files

### Step 5 — Load Procedural Files
- Identify which workflows are relevant to today's task
- Load only those specific procedural files
- Do not load all of `procedural/` — load on task relevance only
- Hard limit: maximum 2 procedural files (plus CLAUDE.md already loaded)

### Step 6 — Load Prospective Tasks
- Load all active prospective tasks with `status: active`
- Prioritise tasks with deadlines within 14 days
- Hard limit: maximum 5 prospective task files

### Step 7 — Load Retrieval Files (on demand only)
- Only load if the session topic explicitly requires external reference material
- Use tag matching against retrieval file tags
- Hard limit: maximum 3 retrieval files
- Do not load retrieval files speculatively

### Step 8 — Do Not Load
- `archive/` contents — load only on explicit user request
- `_inbox/` contents — unvalidated, not ready for use
- `_inbox/review/` — batch review pile, not session context
- All procedural files not relevant to today's task

### Step 9 — Register Session
- Append session start entry to `_meta/models.md`
- Log `SESSION_START` with model name, date, and files loaded count

### Step 10 — Output Context Summary
Before the user begins work, output a brief context summary:
- Project name and current status
- Key facts loaded from semantic memory
- Most recent session date and what was covered
- Active prospective tasks
- Any Tier 2 items in inbox awaiting approval
- Vault health rating from last lint pass

---

## Context Window Budget

| Memory Type | Max Files | Priority |
|---|---|---|
| _meta (CLAUDE.md + index.md) | 2 | Always |
| Semantic (scored 3+) | 7 | High |
| Episodic (recent) | 3 | High |
| Procedural (task-relevant) | 2 | Medium |
| Prospective (active) | 5 | Medium |
| Retrieval (on demand) | 3 | Low |
| **Total maximum** | **22** | — |

---

## Session Continuity — Resume Protocol

If a previous session was interrupted (continuity snapshot found in `_inbox/`):

1. Load the continuity snapshot first — before any other memory files
2. Report to user: "A previous session was interrupted on [date]. It covered [topic] and was working on [next action]. Resume or start fresh?"
3. If resume: load snapshot context as priority working memory, then load regular scored files
4. If fresh start: archive the snapshot, proceed with standard assembly

---

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-10-08 | system | Initial version |
| 2.0 | 2026-10-08 | system | V2 — relevance scoring system, hard file limits, session topic identification, continuity snapshot protocol, context summary output |
