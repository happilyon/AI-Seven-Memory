---
id: proc-memory-assembly
type: procedural
title: Working Memory Assembly Workflow
created: 2026-09-15
updated: 2026-09-15
author: system
confidence: high
ttl: permanent
status: active
tags: [working-memory, session, assembly]
related: [proc-lint-pass, proc-inbox-promotion]
source: system
---

# Workflow: Working Memory Assembly

## Purpose

Defines how an AI model or agent assembles its working memory (context window) at the start of each session. Selective loading — not bulk loading — is the core principle.

## Trigger Condition

- Start of every new session or inference call

## Steps

1. **Always load first (mandatory)**
   - `_meta/CLAUDE.md` — rules and schema
   - `_meta/index.md` — vault catalog for navigation
   - `_meta/models.md` — register this session

2. **Load semantic memory**
   Based on session topic, user identity, or task type:
   - Identify relevant tags or entity names
   - Load matching semantic files with `status: active`
   - Limit: load top 5–10 most relevant files to avoid context bloat

3. **Load recent episodic memory**
   - Load the 3–5 most recent episodic files (by `created` date)
   - Prioritize episodes tagged with the current session's topic
   - Skip episodes older than 90 days (they should already be compressed to semantic)

4. **Load relevant procedural memory**
   - Identify which workflows apply to this session's task
   - Load only those procedural files — not all of `procedural/`

5. **Load active prospective tasks**
   - Scan `prospective/` for tasks with `status: active`
   - Load tasks relevant to this session or with imminent deadlines

6. **Load retrieval docs (if applicable)**
   - Only load if the session requires external reference material
   - Use tag matching or keyword search on `retrieval/` filenames and titles
   - Load top 3–5 most relevant retrieval files

7. **Do not load**
   - `archive/` contents (historical only, load on explicit request)
   - `_inbox/` contents (unvalidated, not ready for use)
   - Parametric memory (it's in the model weights — it's already present)

8. **Register session**
   Append session start entry to `_meta/models.md` Active Sessions table.
   Log `SESSION_START` in `_meta/log.md`.

9. **On session end**
   - Write any new memories to `_inbox/` for lint promotion
   - Update episodic log for this session
   - Update `_meta/models.md` session entry with end time and files read/written
   - Log `SESSION_END` in `_meta/log.md`

## Context Window Budget Guide

| Memory Type | Max Files to Load | Priority |
|---|---|---|
| _meta (schema + index) | 2 | Always |
| Semantic | 5–10 | High |
| Episodic (recent) | 3–5 | High |
| Procedural (relevant) | 1–3 | Medium |
| Prospective (active) | all active | Medium |
| Retrieval | 3–5 | Low (on demand) |

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-09-15 | system | Initial release |
