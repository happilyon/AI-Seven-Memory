# CLAUDE.md — System Schema & Rules Engine
> **Read this first.** This file is the procedural memory of the vault. Every AI model or agent operating inside this vault must read and follow these rules before reading or writing any file.

---

## What This Vault Is

This is a **single-project AI memory vault** — a model-agnostic, persistent memory system built on plain markdown files. It implements all 7 cognitive memory types as a structured folder system. Any AI model (Claude, GPT, Gemini, local models) or human can read from and write to this vault using the rules defined here.

**One vault. One project. Always.**
Each vault is dedicated to a single project only. For every new project, clone a fresh copy of the vault template. This keeps each vault focused, manageable, and clean.

---

## Vault Structure

```
vault/
├── _meta/              → System files (this file, index, log, models registry)
├── _inbox/             → Staging area — all new memory lands here first
│   └── review/         → Batch review pile — low confidence items land here
├── semantic/           → Durable facts, user preferences, world knowledge
├── episodic/           → Dated events, conversation logs, past outcomes
├── procedural/         → Workflows, how-to rules, repeatable processes
├── retrieval/          → Reference documents, external knowledge, RAG sources
├── prospective/        → Future intentions, task queues, scheduled actions
└── archive/            → Expired or superseded memories (never deleted outright)
```

### Memory Type Mapping

| Folder | Memory Type | What Goes Here |
|---|---|---|
| `semantic/` | Semantic | Facts, preferences, user profile, named entities |
| `episodic/` | Episodic | Dated logs of events, sessions, outcomes, decisions |
| `procedural/` | Procedural | Step-by-step workflows, rules, repeatable processes |
| `retrieval/` | Retrieval | External docs, reference material, RAG-ready content |
| `prospective/` | Prospective | TODOs, scheduled tasks, future intentions |
| *(context window)* | Working | Assembled per inference call — never stored as a file |
| *(model weights)* | Parametric | Frozen in the model — never stored as a file |

---

## Frontmatter Schema

**Every file in this vault must include this frontmatter block at the top.**

```yaml
---
id: unique-slug-here
type: semantic | episodic | procedural | retrieval | prospective
title: Human readable title
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: model-name or human
confidence: high | medium | low
source_type: USER | AI | EXTERNAL_SOURCE | INFERENCE
ttl: 30d | 90d | 365d | permanent
status: active | stale | archived | pending | review
tags: [tag1, tag2]
related: [other-file-slug, another-slug]
source: url or origin description
---
```

### Field Definitions

- **id** — unique kebab-case identifier, used for cross-referencing via `related:`
- **type** — which of the 7 memory types this file represents
- **author** — which model or human created/last modified this file
- **confidence** — how reliable this memory is: `high`, `medium`, or `low`
- **source_type** — origin of this memory: USER / AI / EXTERNAL_SOURCE / INFERENCE
- **ttl** — time-to-live from `created` date; after expiry file moves to `archive/`
- **status** — `active` (in use), `stale` (ttl expired), `archived`, `pending` (in _inbox/), `review` (in _inbox/review/)
- **related** — explicit links to other memory files by their `id` field
- **source** — where this memory originated (URL, session name, model name, etc.)

### TTL Guidelines by Memory Type

| Type | Recommended TTL |
|---|---|
| Semantic (stable facts) | `permanent` or `365d` |
| Semantic (volatile facts) | `30d` |
| Episodic | `90d` then compress to semantic summary |
| Procedural | `permanent` |
| Retrieval | `30d` to `90d` depending on source volatility |
| Prospective | Expires on task completion or deadline date |

---

## Tiered Promotion System

Every item arriving in `_inbox/` is assessed against three criteria before any human sees it. This prevents approval fatigue and keeps the human review queue meaningful.

### Tier 1 — Auto-Promote (no human approval needed)
All three conditions must be met:
- `confidence: high`
- `source_type: USER` or `source_type: EXTERNAL_SOURCE`
- No duplicate of existing vault content detected

Auto-promoted items go directly to the correct memory folder with `status: active`. Log `AUTO-PROMOTE` action.

### Tier 2 — Human Approval Queue
Items that do not meet Tier 1 but are worth reviewing this session:
- `confidence: medium` with `source_type: USER`
- Any item with `source_type: AI` regardless of confidence
- Any item flagged as DUPLICATE_CANDIDATE
- Any item containing a CONTRADICTION

Present these to the human at session close. Human approves, edits, or rejects each one.

### Tier 3 — Batch Review Pile
Items that do not need immediate attention:
- `confidence: low`
- `source_type: INFERENCE`
- Items flagged UNVERIFIED
- Episodic compression candidates

Move to `_inbox/review/` with `status: review`. Human reviews these weekly, not per session.

---

## Workflow Rules

### Rule 1 — Inbox First
Nothing writes directly to memory folders. All new content lands in `_inbox/` with `status: pending`. The tiered promotion system then routes it automatically.

### Rule 2 — Topic-Chunked Files
Do not create one file per fact. Do not create one file for everything. Group facts that would always be needed together into a single topic-chunked file.

### Rule 3 — Topic Map Before Writing
Before creating any memory files for a new project, the AI model must:
1. Analyse the project context
2. Propose a topic map — filenames and one-line descriptions
3. Present for human approval
4. Only create files after approval
The approved topic map is recorded in `_meta/index.md`.

### Rule 4 — Templates Are Read-Only Masters
`TEMPLATE.md` files are permanent master copies. Always duplicate and rename before filling in. Never modify TEMPLATE.md directly.

### Rule 5 — Cross-Reference Explicitly
When a new memory relates to an existing one, add cross-references bidirectionally via the `related:` field.

### Rule 6 — Never Delete, Archive
When a memory expires or is superseded, move it to `archive/`. Raw history is always preserved.

### Rule 7 — Episodic Compression Requires Quality Gate
After 90 days, episodic logs are candidates for compression into semantic memory. The compression is NOT automatic. The lint pass flags candidates and presents a compression summary to the human for approval before any episodic content becomes permanent semantic memory.

### Rule 8 — Procedural Memory Is Version-Controlled
Files in `procedural/` are never auto-modified at runtime. Changes require deliberate human review. All changes must update the `updated:` frontmatter field and version history table.

### Rule 9 — Log Every Action
Every read, write, lint, promote, archive, or review action must be appended to `_meta/log.md`. The log is pruned to a rolling 90-day window — older entries are summarised and archived monthly.

### Rule 10 — Author Attribution
The `author:` field must always reflect which model or human last modified the file.

### Rule 11 — AI-Seeded Memory Requires Human Review
All AI-generated initial memory files go to `_inbox/` with `status: pending`. No AI-generated memory enters active folders without human sign-off.

### Rule 12 — Extraction Files Are Multi-Type
Extraction files use `type: extraction`. The lint pass processes them section by section, not as a single unit, routing each section to the correct memory folder.

### Rule 13 — Source Attribution Is Mandatory
Every item must carry its `source_type` field. Never elevate an AI recommendation to a stored fact. Never treat INFERENCE as CONFIRMED without human sign-off.

### Rule 14 — STATE Memory Is Temporary
STATE memory items store current project status and must never use TTL: permanent or TTL: 365d. Maximum TTL for STATE items is 30d.

### Rule 15 — One Project Per Vault
Each vault is dedicated to a single project only. Do not add unrelated projects. For each new project clone a fresh vault template.

### Rule 16 — Tiered Promotion Is Mandatory
The tiered promotion system (Tier 1 auto-promote, Tier 2 human approval, Tier 3 batch review) must be applied to every item in `_inbox/`. Never send every item to the human approval queue. Only Tier 2 items require human attention per session.

### Rule 17 — Lint Pass Checkpoints
The lint pass must complete each step and log a checkpoint before moving to the next. If a step fails or is incomplete, the lint pass stops at that checkpoint and reports the failure. It does not proceed silently.

### Rule 18 — Memory Assembly Relevance Scoring
When assembling working memory, files are scored for relevance against the session topic before loading. Only files scoring above the relevance threshold are loaded. Relevance is determined by: tag overlap with session topic, recency of `updated:` date, and explicit `related:` links to already-loaded files.

### Rule 19 — Session Continuity
If a session is interrupted or the context window is exceeded, the AI must write a continuity snapshot to `_inbox/` before closing. The snapshot captures current session state, decisions made so far, and the next intended action. The "resume session" prompt loads this snapshot to continue.

### Rule 20 — TTL Renewal
When a file reaches its TTL expiry and is still valid, it must be explicitly renewed rather than left as stale. Renewal updates the `created:` date to today, resets the TTL, and logs a `RENEW` action. A stale file that is not renewed within 14 days of expiry is automatically archived.

---

## Standard Session Prompting Loop

Every session follows this 5-prompt sequence.

### Prompt 1 — Start Session
> "Start a new session. Read CLAUDE.md and index.md, assemble my working memory using relevance scoring, and summarise what you know about this project so far."

### Prompt 2 — Work
Normal conversation, task, or question.

### Prompt 3 — Capture
> "What new facts, decisions, or insights from this session should be saved? Apply the tiered promotion system — tell me what will auto-promote, what needs my approval, and what goes to batch review."

### Prompt 4 — Review
> "Show me only the Tier 2 items that need my decision. Present each one with its confidence rating and source type."

### Prompt 5 — Close Session
> "Approved. Write confirmed items to _inbox/, move Tier 3 items to _inbox/review/, run the lint pass with checkpoints, and close this session."

---

## New Project Initialisation Loop

Run once per new vault before the standard session loop begins.

### Init Prompt 1 — Seed Request
> "This is a new [project type]. Here is the context: [paste details]. Propose a topic map. Do not create any files yet."

### Init Prompt 2 — Topic Map Review
Human reviews, edits, and approves.

### Init Prompt 3 — Generate Memory
> "Topic map approved. Generate initial semantic memory files and write to _inbox/. Apply tiered promotion — flag what needs my review."

### Init Prompt 4 — Review Generated Files
> "Show me the Tier 2 items for approval. Auto-promoted items can be listed as a summary only."

### Init Prompt 5 — Commit
> "All approved. Run the lint pass with checkpoints, update index.md, and confirm vault is ready."

---

## Lint Pass Protocol

Run daily for active systems, weekly for low-activity vaults. Each step logs a checkpoint before proceeding.

**Step 1 — CHECKPOINT: Load schema**
Read CLAUDE.md. Log checkpoint.

**Step 2 — CHECKPOINT: TTL scan**
Flag expired files as stale. Do not archive automatically. Log checkpoint.

**Step 3 — CHECKPOINT: Tiered inbox processing**
Apply Tier 1/2/3 rules to all `_inbox/` items:
- Tier 1 → auto-promote to memory folder
- Tier 2 → add to human approval summary
- Tier 3 → move to `_inbox/review/`
Log checkpoint.

**Step 4 — CHECKPOINT: Conflict detection**
Scan semantic/ for contradictions. Resolve newer-wins or flag for human review. Log checkpoint.

**Step 5 — CHECKPOINT: Episodic compression candidates**
Flag episodic files older than 90 days. Generate compression summary. Present to human for approval before any compression occurs. Log checkpoint.

**Step 6 — CHECKPOINT: Orphan detection**
Flag files with no related: links and not in index.md. Log checkpoint.

**Step 7 — CHECKPOINT: Prospective tasks**
Archive completed tasks. Flag overdue tasks as stale. Log checkpoint.

**Step 8 — CHECKPOINT: Stale file renewal check**
Files stale for more than 14 days with no renewal → auto-archive. Log checkpoint.

**Step 9 — CHECKPOINT: Log pruning**
Summarise log entries older than 90 days into a monthly summary entry. Archive raw entries. Log checkpoint.

**Step 10 — CHECKPOINT: Rebuild index**
Update _meta/index.md with current vault state and health summary. Log checkpoint.

**Step 11 — CHECKPOINT: Write lint summary**
Append full lint summary to log.md. Report: auto-promoted, tier2-queued, tier3-review, conflicts, compression-candidates, orphans, archived, renewed, log-pruned.

---

## Working Memory Assembly

Assemble working memory at session start using relevance scoring:

1. Always load: `_meta/CLAUDE.md` + `_meta/index.md`
2. Score all semantic files against session topic — load top 5 scoring files only
3. Load 3 most recent episodic files tagged to this project
4. Load procedural files explicitly relevant to today's task
5. Load active prospective tasks with imminent deadlines first
6. Load retrieval files only if session topic requires external reference

**Relevance scoring criteria:**
- Tag overlap with session topic: +3 points per matching tag
- Updated within last 7 days: +2 points
- Directly linked via related: to already-loaded file: +2 points
- Created within last 30 days: +1 point
- Load files scoring 3 or above only

---

## Vault Health Check

Run on demand without triggering a full lint pass:

> "Run a vault health check — show me: inbox count, review pile count, stale file count, overdue prospective tasks, last lint pass date, and overall health rating."

Health ratings:
- **Green** — inbox empty, no stale files, lint pass within 7 days
- **Amber** — inbox has items, some stale files, lint pass within 14 days
- **Red** — inbox overflowing, many stale files, lint pass overdue

---

## Agent Autonomy Levels

| Level | Name | Agent Does Autonomously | Human Role |
|---|---|---|---|
| 1 | Assisted | Proposes all actions, applies tiered promotion, writes nothing without approval | Reviews Tier 2 queue only |
| 2 | Semi-autonomous | Auto-promotes Tier 1, writes Tier 2 to inbox, runs lint, flags Tier 3 for batch | Reviews batch pile weekly |
| 3 | Autonomous | Full cycle including Tier 2 decisions, notifies human of summary only | Reviews session summaries |

**Default: Level 1.**

---

## Multi-Model Protocol

- Each model registers in `_meta/models.md` on session start
- All writes go through `_inbox/` — never directly to memory folders
- Write conflicts resolved by timestamp — newer wins
- Episodic logs are append-only
- Source attribution mandatory on every file

---

## Chat Knowledge Extraction Workflow

Extraction files use `type: extraction` and are processed section by section.
See `CHAT-EXTRACTION-PROMPT.md` for the full extraction prompt.

Tiered promotion applies to extraction files:
- HIGH confidence USER facts → Tier 1 auto-promote
- AI-sourced or MEDIUM confidence → Tier 2 human approval
- INFERENCE or LOW confidence → Tier 3 batch review

---

## Use Case Management

5 built-in use cases defined in `procedural/new-project-init.md` and `SYSTEM-EXPLAINER.md` Section 11. Both files must stay in sync. Use cases are extensible — add new ones via the prompt in this file's Use Case Management section.

---

## Future Roadmap

- V3 — Vector search layer for semantic similarity retrieval
- V3 — Automated lint agent running on schedule without human prompting
- V4 — Web/mobile interface with visual approval queue
- V4 — SaaS deployment with hosted vault per project

---

## Versioning

**Current version:** `2.0.0`

## Changelog

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0.0 | 2026-10-08 | system | Initial release |
| 2.0.0 | 2026-10-08 | system | V2 — tiered promotion system, lint pass checkpoints, relevance scoring, session continuity, episodic compression quality gate, vault health check, TTL renewal mechanism, log pruning, single project rule, 20 rules total |
