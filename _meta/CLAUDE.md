# CLAUDE.md — System Schema & Rules Engine
> **Read this first.** This file is the procedural memory of the vault. Every AI model or agent operating inside this vault must read and follow these rules before reading or writing any file.

---

## What This Vault Is

This is a **universal AI memory vault** — a model-agnostic, persistent memory system built on plain markdown files. It implements all 7 cognitive memory types as a structured folder system. Any AI model (Claude, GPT, Gemini, local models) or human can read from and write to this vault using the rules defined here.

It is inspired by:
- The 7 memory types framework for agentic AI systems
- Andrej Karpathy's llm-wiki pattern (compounding knowledge via structured markdown)
- The principle that memory should be staged, lifecycle-managed, and model-agnostic

---

## Vault Structure

```
vault/
├── _meta/              → System files (this file, index, log, models registry)
├── _inbox/             → Staging area — all new memory lands here first
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
ttl: 30d | 90d | 365d | permanent
status: active | stale | archived | pending
tags: [tag1, tag2]
related: [other-file-slug, another-slug]
source: url or origin description
---
```

### Field Definitions

- **id** — unique kebab-case identifier, used for cross-referencing via `related:`
- **type** — which of the 7 memory types this file represents
- **author** — which model or human created/last modified this file
- **confidence** — how reliable this memory is; low-confidence files are lint candidates
- **ttl** — time-to-live from `created` date; after expiry, file moves to `archive/`
- **status** — `active` (in use), `stale` (ttl expired, pending review), `archived` (moved to archive/), `pending` (in _inbox/, not yet promoted)
- **related** — explicit links to other memory files by their `id` field
- **source** — where this memory originated (URL, session name, model name, etc.)

### TTL Guidelines by Memory Type

| Type | Recommended TTL |
|---|---|
| Semantic (stable facts) | `permanent` or `365d` |
| Semantic (volatile facts, e.g. prices) | `30d` |
| Episodic | `90d` then compress to semantic summary |
| Procedural | `permanent` (update deliberately, never auto-expire) |
| Retrieval | `30d` to `90d` depending on source volatility |
| Prospective | Expires on task completion or deadline date |

---

## Workflow Rules

### Rule 1 — Inbox First
Nothing writes directly to memory folders. All new content lands in `_inbox/` with `status: pending`. The lint pass promotes, merges, or rejects it.

### Rule 2 — Topic-Chunked Files
Do not create one file per fact. Do not create one file for everything. Group facts that would always be needed together into a single topic-chunked file. Facts that would ever be needed independently belong in separate files.

### Rule 3 — Topic Map Before Writing
Before creating any memory files for a new project, the AI model must:
1. Analyze the project context provided by the human
2. Propose a topic map — a list of proposed file names and what each will contain
3. Present the topic map to the human for approval or editing
4. Only create memory files after the topic map is approved
The approved topic map is recorded in `_meta/index.md` as the project's unique structure.

### Rule 4 — Templates Are Read-Only Masters
`TEMPLATE.md` files in every folder are permanent master copies. Never fill in or modify a TEMPLATE.md directly. Always duplicate it, rename the copy, then fill in the copy. The blank master must always remain intact for future use.

### Rule 5 — Cross-Reference Explicitly
When a new memory relates to an existing one, add the existing file's `id` to the `related:` field of the new file — and update the existing file's `related:` field to point back.

### Rule 6 — Never Delete, Archive
When a memory expires or is superseded, move it to `archive/` and update its `status: archived`. Raw history is preserved. Only the lint pass promotes archiving.

### Rule 7 — Episodic Compression
After 90 days, episodic logs should be summarized into a semantic memory file. The original episodic file moves to `archive/`. The new semantic file's `source:` field should reference the original episodic file's `id`.

### Rule 8 — Procedural Memory Is Version-Controlled
Files in `procedural/` are never auto-modified by an AI agent at runtime. Changes require deliberate human or scheduled agent review. All changes must update the `updated:` frontmatter field.

### Rule 9 — Log Every Action
Every read, write, lint, or archive action must be appended to `_meta/log.md`. See log format below.

### Rule 10 — Author Attribution
The `author:` field must always reflect which model or human last modified the file. In multi-model sessions, this is critical for provenance tracking.

### Rule 11 — AI-Seeded Memory Requires Human Review
When an AI model generates initial semantic memory files from project context, all generated files go to `_inbox/` with `status: pending`. The human must review and approve or edit before lint promotion. No AI-generated memory enters active folders without human sign-off on first seed.

---

## Standard Session Prompting Loop

Every session — regardless of project, user, or AI model — follows this 5-prompt sequence. This is the core UX of the system.

### Prompt 1 — Start Session
> "Start a new session. Read CLAUDE.md and index.md, assemble my working memory, and summarise what you know about this project so far."

### Prompt 2 — Work
Normal conversation, task, or question. The AI works using loaded memory as context. No special prompt needed.

### Prompt 3 — Capture
> "What new facts, decisions, or insights from this session should be saved to memory? Propose what to write and which files to update, following the topic-chunking rules."

### Prompt 4 — Review
> "Show me exactly what you're about to write before saving anything. I'll approve, edit, or reject each item."

### Prompt 5 — Close Session
> "Approved. Write the confirmed items to _inbox/, update the episode log, run the lint pass, and close this session."

---

## New Project Initialisation Loop

When starting a brand new project vault, run this sequence once before the standard session loop begins.

### Init Prompt 1 — Seed Request
> "This is a new [project type]. Here is the context: [paste all relevant project details]. Analyse this context and propose a topic map for the semantic memory files — list each proposed filename and a one-line description of what it will contain. Do not create any files yet."

### Init Prompt 2 — Topic Map Review
Human reviews, edits, and approves the proposed topic map.

### Init Prompt 3 — Generate Memory
> "Topic map approved. Now generate the initial semantic memory files based on the approved topic map and write them all to _inbox/ for my review."

### Init Prompt 4 — Review Generated Files
> "Show me each generated file one by one. I'll approve, edit, or reject each one."

### Init Prompt 5 — Commit
> "All approved. Run the lint pass to promote the approved files, update index.md with the project topic map, and confirm the vault is ready."

---

## Lint Pass Protocol

Run the lint pass on a scheduled basis (daily for active systems, weekly for low-activity vaults).

**The lint pass must:**
1. Scan all files for expired TTL (`created` + `ttl` < today) → set `status: stale`, flag for review
2. Promote `_inbox/` files that are valid and non-duplicate → move to correct memory folder, set `status: active`
3. Reject `_inbox/` files that duplicate existing memories → merge or discard
4. Detect conflicts — newer semantic files that contradict older ones → resolve in favor of newer, archive older
5. Compress episodic files older than 90 days → summarize to semantic, archive original
6. Identify orphan files (no `related:` links, not referenced by index.md) → flag for review
7. Update `_meta/index.md` to reflect current vault state
8. Append lint summary to `_meta/log.md`

---

## Working Memory Assembly (Per Inference Call)

When an AI model begins a session, it should assemble working memory by loading:

1. This file (`_meta/CLAUDE.md`) — always load first
2. `_meta/index.md` — to understand vault contents and topic map
3. Relevant `semantic/` files — based on session topic/user
4. Most recent `episodic/` entries — last 3–5 sessions
5. Relevant `procedural/` files — workflows needed for this session
6. Active `prospective/` tasks — pending intentions relevant to session
7. `retrieval/` files — only if RAG lookup returns relevant matches

**Do not load the entire vault into context.** Load selectively based on session relevance.

---

## Multi-Model Protocol

When multiple AI models are operating in the same vault:

- Each model must read `_meta/models.md` before writing anything
- Each model must register its session in `_meta/models.md` on start
- Write conflicts are resolved by timestamp — most recent write wins for semantic facts
- Episodic logs are append-only — never overwrite another model's episode entry
- All writes must go through `_inbox/` — no model writes directly to memory folders

---

## Agent Autonomy Levels

This system supports three levels of agent autonomy. The level is set per project in `_meta/index.md`.

| Level | Name | What The Agent Does Autonomously | Human Role |
|---|---|---|---|
| 1 | Assisted | Proposes all actions, writes nothing without approval | Approves every write |
| 2 | Semi-autonomous | Writes to _inbox/ freely, runs lint, flags conflicts for human | Reviews flagged items only |
| 3 | Autonomous | Full read/write/lint cycle, notifies human of summary only | Reviews session summaries |

**Default for new projects: Level 1.** Increase autonomy level deliberately after trust is established.

---

## Future Roadmap (Locked In Design)

The following capabilities are designed for but not yet implemented. Any agent or developer building on this system should preserve compatibility with these future states:

- **Vector search layer** — embedding index on top of vault files for semantic similarity retrieval
- **Autonomous agent** — scheduled agent that runs session loop, lint, and episodic compression without human prompting
- **Multi-agent orchestration** — specialist agents (research, coding, writing, communication) sharing one vault via the Memory Manager pattern
- **Web/mobile interface** — human-readable vault browser with approve/reject UI for inbox items
- **SaaS deployment** — hosted vault per user/team, model-agnostic API, one-click project templates for the 5 core use cases

---

---

## Chat Knowledge Extraction Workflow

The vault supports importing knowledge extracted from external AI chats on any platform. This is handled via `CHAT-EXTRACTION-PROMPT.md` in the vault root. The extraction prompt is at version 2.0.0 and produces a significantly richer output than v1.0.0.

### Extraction File Identification

Extraction files are identified by `type: extraction` in their frontmatter.
They contain knowledge for multiple memory folders and must be processed
section by section — not as a single unit.

### When An Extraction File Arrives In _inbox/

When the lint pass finds a file with `type: extraction`, it must:

**Promote by section to correct memory folders:**
1. Semantic facts (HIGH confidence, Source: USER only) → `semantic/`
2. State memory items → `semantic/` with TTL: 30d
3. Episodic narrative → `episodic/`
4. Workflows and processes → `procedural/`
5. Resources and references → `retrieval/`
6. Tasks, open questions, prospective items → `prospective/`
7. User preferences (HIGH confidence) → `semantic/` with TTL: permanent
8. AI HANDOFF yaml block → attach to episodic file as front matter
9. Next Session Primer → attach to episodic file as final section

**Flag for human review before promotion:**
- All MEDIUM and LOW confidence items
- All items with Source: INFERENCE
- All items with Source: AI (verify before treating as fact)
- All UNVERIFIED CLAIMS
- All CONTRADICTION items
- All DUPLICATE_CANDIDATE items
- Any item where Extraction Quality Check shows FAIL

**Never promote:**
- ASSUMPTIONS & INFERENCES without explicit human confirmation
- UNVERIFIED CLAIMS as confirmed facts
- AI recommendations as user decisions
- Any item where secrets scanning showed YES without confirming redaction

### STATE Memory Type

Extraction files introduce a 6th storable memory type: STATE.
STATE captures current project status, active configuration, current blockers,
and decisions currently in force. It is distinct from durable semantic memory
because it is temporary and will change.

STATE items are stored in `semantic/` with TTL: 30d and tagged `[state, current]`
so they can be identified and refreshed separately from permanent semantic facts.

### Source Attribution

Every promoted item must preserve its source attribution in the file:
- USER — explicitly stated by the user
- AI — generated or recommended by the AI model
- EXTERNAL_SOURCE — from a URL or document cited in the chat
- INFERENCE — inferred from context, never explicitly stated

Never strip source attribution when promoting to memory folders.

### Stable Item IDs

Extraction files assign stable IDs to items (fact-001, decision-001, task-001).
Preserve these IDs when promoting to memory folders. They enable deduplication,
cross-referencing, and automated update tracking across future extractions.

### Secrets And PII

If the extraction file's SENSITIVE DATA DETECTED section contains any entries,
confirm all secrets are redacted before promoting any content from that file.
Never promote a file containing unredacted secrets or PII.

### Rule 12 — Extraction Files Are Multi-Type
Unlike standard inbox files which map to one memory folder, extraction files
map to multiple folders. The lint pass must process them section by section.
Extraction files use `type: extraction` not `type: episodic`.

### Rule 13 — Source Attribution Is Mandatory
Every item promoted from an extraction file must carry its source attribution
(USER/AI/EXTERNAL_SOURCE/INFERENCE). Never elevate an AI recommendation to
a stored fact. Never treat INFERENCE as CONFIRMED without human sign-off.

### Rule 14 — STATE Memory Is Temporary
STATE memory items must never be stored with TTL: permanent or TTL: 365d.
Maximum TTL for STATE items is 30d. They represent current project state
that will change, not durable knowledge.

---

### Rule 15 — One Project Per Vault
Each vault is dedicated to a single project only. Do not combine multiple
projects inside one vault. For each new project create a new vault from
the template. Personal preferences and user profile facts that apply across
projects should be seeded fresh into each new vault during the init loop.

## Use Case Management

The vault ships with 5 built-in use cases. These are not hardcoded — they are living, extensible content that should grow as the system is adopted for new project types.

### Where Use Cases Live

| File | What To Update |
|---|---|
| `procedural/new-project-init.md` | Topic map examples for each use case — the primary definition |
| `SYSTEM-EXPLAINER.md` Section 11 | Plain language description of each use case — must stay in sync |

**These two files must always match.** If you add or edit a use case in one, update the other in the same operation.

### The 5 Built-In Use Cases

1. Personal AI Assistant
2. Software Development Project
3. Research & Knowledge Building
4. Client & Business Management
5. Content Creation & Writing

### Adding A New Use Case

Use this prompt to instruct the AI agent to add a new use case:

> "Read procedural/new-project-init.md and SYSTEM-EXPLAINER.md. I want to add a new use case called [name]. Here is the context: [describe the use case, its goals, and what should be remembered]. Propose a topic map for it, add it to the use case examples in new-project-init.md, update Section 11 in SYSTEM-EXPLAINER.md to include the new use case description, increment the version in CLAUDE.md, and write all changes to _inbox/ for my review."

#### Use Case Update Rules

- All use case edits follow the standard inbox-first rule — write to `_inbox/`, human reviews before lint promotes
- Both `new-project-init.md` and `SYSTEM-EXPLAINER.md` must be updated in the same inbox batch
- Existing use case topic maps should be refined over time as real project experience reveals better chunking strategies
- When a use case is significantly revised, increment the CLAUDE.md version and log the change

---

## Versioning This Schema

This file (`CLAUDE.md`) is versioned. When making structural changes, increment the version and append a changelog entry below.

**Current version:** `1.0.0`

---

## Changelog

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0.0 | 2026-09-15 | system | Initial release — single project per vault scope, 14 rules, 7 memory types, chat extraction workflow, multi-model support, agent autonomy levels |
