# Universal AI Memory Vault — Complete System Documentation

> **Version:** 1.0.0 | **Created:** 2026-09-15 | **Updated:** 2026-09-15 | **Schema:** See `_meta/CLAUDE.md`
>
> This document is the human-readable and AI-readable guide to the entire memory system. Read this before interacting with or building on the vault. It explains the *why* behind every design decision, not just the *what*.

---

## Table of Contents

1. [What This System Is](#1-what-this-system-is)
2. [The Problem It Solves](#2-the-problem-it-solves)
3. [The 7 Memory Types](#3-the-7-memory-types)
4. [Architecture Overview](#4-architecture-overview)
5. [Folder Structure](#5-folder-structure)
6. [The Frontmatter Schema](#6-the-frontmatter-schema)
7. [Topic-Chunked File Strategy](#7-topic-chunked-file-strategy)
8. [Template Duplication Rule](#8-template-duplication-rule)
9. [The Standard Session Prompting Loop](#9-the-standard-session-prompting-loop)
10. [New Project Initialisation Loop](#10-new-project-initialisation-loop)
11. [The 5 Core Use Cases](#11-the-5-core-use-cases)
12. [Memory Lifecycle](#12-memory-lifecycle)
13. [The Lint Pass](#13-the-lint-pass)
14. [Working Memory Assembly](#14-working-memory-assembly)
15. [Agent Autonomy Levels](#15-agent-autonomy-levels)
16. [Multi-Model Operations](#16-multi-model-operations)
17. [How To Use This As A Template](#17-how-to-use-this-as-a-template)
17b. [Chat Knowledge Extraction](#17b-chat-knowledge-extraction)
18. [Future Roadmap](#18-future-roadmap)
19. [Design Principles & Philosophy](#19-design-principles--philosophy)
20. [Known Limitations](#20-known-limitations)
21. [Glossary](#21-glossary)

---

## 1. What This System Is

The Universal AI Memory Vault is a **model-agnostic, persistent memory system** built entirely on plain markdown files organised in a structured folder hierarchy.

It implements all **7 cognitive memory types** drawn from cognitive science and applied AI systems research. It is designed to be:

- **Universal** — works as a template for any project, system, or use case
- **Model-agnostic** — any AI model (Claude, GPT, Gemini, local models) can read and write using the same schema
- **Persistent** — memory survives across sessions, model changes, and system restarts
- **Self-managing** — includes lifecycle rules that keep memory accurate and fresh over time
- **Human-readable** — every memory is a markdown file a human can open, read, and edit
- **Easy to use** — a standard 5-prompt session loop means anyone can run it from day one

It is **not** a vector database, not a cloud service, and not tied to any specific AI platform. The files are the system.

**Single project scope:** Each vault is dedicated to one project only. For every new project, clone a fresh copy of the vault template. This keeps each vault focused, manageable, and clean.

---

## 2. The Problem It Solves

### The Core Problem: AI Models Are Amnesiac By Default

Every AI model starts each conversation with no memory of past interactions. It knows only what is in its training data (parametric memory) and what you put in the current context window (working memory). Everything else is lost.

This creates three compounding problems:

**Problem 1 — No cross-session continuity.** The model doesn't remember what you discussed last week, what decisions were made, what facts were established, or what tasks are pending.

**Problem 2 — No accumulated knowledge.** Every session starts from zero. Insights, preferences, and learned workflows don't compound over time.

**Problem 3 — Stale knowledge.** Even if you do store memories somewhere, facts change. Prices shift. People change roles. Decisions get reversed. Without lifecycle management, a memory system accumulates false information over time.

### What This System Does About It

- **Semantic and episodic files** provide cross-session continuity
- **The compounding wiki pattern** (inspired by Karpathy's llm-wiki) means every session adds to a growing knowledge base
- **TTL fields and the lint pass** ensure stale memories are flagged, refreshed, or archived automatically
- **The standard prompting loop** means any user can operate the system with 5 repeatable prompts

---

## 3. The 7 Memory Types

This system maps the 7 cognitive memory types to concrete storage mechanisms.

### 3.1 Working Memory
**What it is:** The active context window — what the model is currently thinking about.
**How it's implemented:** Not stored as a file. Assembled fresh at the start of each session by loading relevant files from the vault into the model's context.
**Key characteristic:** Temporary. Exists only for the duration of a session.

### 3.2 Semantic Memory
**What it is:** Durable facts, knowledge, and beliefs about the world, a user, a project, or a domain.
**How it's implemented:** Topic-chunked markdown files in `semantic/`. Related facts grouped together per topic.
**Examples:** User preferences, project facts, domain knowledge, architecture decisions.
**Key characteristic:** Persistent and durable. Updated when facts change, not deleted.

### 3.3 Episodic Memory
**What it is:** Memory of specific past events, sessions, and their outcomes.
**How it's implemented:** Dated markdown files in `episodic/`. One file per week or major theme.
**Examples:** Session logs, key decisions made, what worked, what failed.
**Key characteristic:** Time-stamped and narrative. Compresses into semantic memory after 90 days.

### 3.4 Procedural Memory
**What it is:** Knowledge of how to do things — workflows, rules, repeatable processes.
**How it's implemented:** Markdown files in `procedural/`. Each file is a step-by-step workflow.
**Examples:** The session loop, lint pass, inbox promotion, project init.
**Key characteristic:** Deliberately maintained. Never auto-modified at runtime. Version-controlled.

### 3.5 Retrieval Memory
**What it is:** External documents and reference material pulled in on demand.
**How it's implemented:** Summarised markdown files in `retrieval/`. One file per source or topic area.
**Examples:** Paraphrased API docs, summarised research papers, reference guides.
**Key characteristic:** Time-sensitive. Refreshed when TTL expires.

### 3.6 Parametric Memory
**What it is:** World knowledge frozen into the model's weights during pretraining.
**How it's implemented:** Not stored in the vault. Exists inside the model itself.
**Key characteristic:** Cannot be modified at runtime. Changes only via fine-tuning or model replacement.

### 3.7 Prospective Memory
**What it is:** Memory of future intentions — tasks pending, scheduled actions, follow-ups.
**How it's implemented:** Task files in `prospective/`. One file per project or workstream.
**Examples:** Pending tasks, upcoming deadlines, scheduled memory refreshes.
**Key characteristic:** Action-oriented. Moves to `archive/` on completion.

---

## 4. Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    AI MODEL / AGENT                      │
│              (Claude, GPT, Gemini, Local)                │
└────────────────────────┬────────────────────────────────┘
                         │ guided by 5-prompt session loop
                         ▼
┌─────────────────────────────────────────────────────────┐
│                   WORKING MEMORY                         │
│           (assembled context window per session)         │
│                                                          │
│  ┌──────────┐ ┌──────────┐ ┌────────────┐ ┌─────────┐  │
│  │ semantic │ │ episodic │ │ procedural │ │prospec- │  │
│  │  facts   │ │  recent  │ │ workflows  │ │  tive   │  │
│  └──────────┘ └──────────┘ └────────────┘ └─────────┘  │
└────────────────────────┬────────────────────────────────┘
                         │ reads from / writes to _inbox/
                         ▼
┌─────────────────────────────────────────────────────────┐
│                    OBSIDIAN VAULT                        │
│              (plain markdown files on disk)              │
│                                                          │
│  _meta/    semantic/  episodic/  procedural/             │
│  _inbox/   retrieval/ prospective/  archive/             │
└────────────────────────┬────────────────────────────────┘
                         │ maintained by
                         ▼
┌─────────────────────────────────────────────────────────┐
│               LINT PASS (scheduled)                      │
│     TTL enforcement · conflict resolution · compression  │
└─────────────────────────────────────────────────────────┘
```

### Key Architectural Decisions

**Why markdown files?** Plain text is universal. Any AI model, any tool, any human can read and write markdown. No proprietary format, no database, no API dependency.

**Why Obsidian?** Obsidian treats a folder of markdown files as a first-class knowledge system with graph visualisation, backlinks, and tag search. It's the ideal human interface for the vault. But the vault doesn't require Obsidian — it works with any text editor or file system access.

**Why Claude Code as the orchestration layer?** Claude Code can read and write files directly, run scripts, and operate as the Memory Manager without requiring any additional infrastructure.

**Why a staging inbox?** Direct writes to memory folders create pollution risk, especially in multi-model environments. The `_inbox/` folder ensures every new memory is validated before entering the active vault.

---

## 5. Folder Structure

```
vault/
│
├── SYSTEM-EXPLAINER.md               ← Complete system documentation
├── PROMPTS.md                        ← Step-by-step prompts guide for all users (START HERE)
├── CHAT-EXTRACTION-PROMPT.md         ← Paste into any external AI chat to extract knowledge
│
├── _meta/                            ← System files
│   ├── CLAUDE.md                     ← Schema v2.0.0 — rules engine (READ FIRST)
│   ├── index.md                      ← Vault catalog + project topic map
│   ├── log.md                        ← Append-only action log
│   └── models.md                     ← AI model registry
│
├── _inbox/                           ← Staging area
│   └── TEMPLATE.md                   ← Master template — duplicate, never edit
│
├── semantic/                         ← Durable facts (topic-chunked)
│   └── TEMPLATE.md
│
├── episodic/                         ← Event and session logs
│   └── TEMPLATE.md
│
├── procedural/                       ← Workflows and rules
│   ├── TEMPLATE.md
│   ├── session-loop.md               ← 5-prompt standard session loop
│   ├── new-project-init.md           ← 5-prompt new project init loop
│   ├── lint-pass.md                  ← Lint pass workflow
│   ├── inbox-promotion.md            ← Inbox promotion workflow
│   └── memory-assembly.md            ← Working memory assembly workflow
│
├── retrieval/                        ← External reference docs
│   └── TEMPLATE.md
│
├── prospective/                      ← Task queue
│   └── TEMPLATE.md
│
└── archive/                          ← Expired/superseded memories
```

---

## 6. The Frontmatter Schema

Every file in this vault must include a YAML frontmatter block. This is the metadata layer that makes lifecycle management, routing, and indexing possible.

```yaml
---
id: unique-kebab-case-slug
type: semantic | episodic | procedural | retrieval | prospective
title: Human readable title
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: your-model-name | gpt-4o | human-name
confidence: high | medium | low
ttl: 30d | 90d | 365d | permanent | YYYY-MM-DD
status: active | stale | archived | pending
tags: [tag1, tag2, tag3]
related: [other-file-id, another-file-id]
source: https://url or "session-2026-09-15" or "user-stated"
---
```

### TTL Guidelines

| Type | Recommended TTL |
|---|---|
| Semantic (stable facts) | `permanent` or `365d` |
| Semantic (volatile facts) | `30d` |
| Episodic | `90d` then compress |
| Procedural | `permanent` |
| Retrieval | `30d` to `90d` |
| Prospective | Specific deadline date |

---

## 7. Topic-Chunked File Strategy

### The Problem With Extremes

- **One file per fact** = token explosion as the vault grows; too many files to load selectively
- **One file for everything** = can't load only what's relevant; defeats the purpose of selective loading

### The Right Approach: Topic Chunks

Group facts that would always be needed together into a single file. Facts you'd ever want independently belong in separate files.

```
semantic/
├── user-profile.md        ← all facts about the user
├── project-tech-stack.md  ← all tech decisions
├── client-account.md      ← all client facts
└── domain-knowledge.md    ← all domain context
```

### The Topic Map Rule

Before creating any memory files for a new project, the AI must:
1. Analyse the project context
2. Propose a topic map (list of filenames + one-line descriptions)
3. Get human approval or edits
4. Only then create files

The approved topic map is recorded in `_meta/index.md` as the project's unique structure. Topic maps differ per project — a software project clusters differently to a research project or personal assistant.

---

## 8. Template Duplication Rule

Every memory folder contains a `TEMPLATE.md` file. This is a **read-only master copy**.

**The rule:**
1. Duplicate the TEMPLATE.md file
2. Rename the duplicate to your chosen filename
3. Fill in the renamed copy
4. Never modify TEMPLATE.md itself

This ensures a blank master is always available for future entries. The AI model must follow this rule. Humans must follow this rule.

---

## 9. The Standard Session Prompting Loop

This is the core UX of the system. Every session follows the same 5-prompt sequence.

| Prompt | What To Say | What Happens |
|---|---|---|
| **1 — Start** | "Start a new session. Read CLAUDE.md and index.md, assemble my working memory, and summarise what you know about this project so far." | AI loads memory, outputs context summary |
| **2 — Work** | *(normal conversation)* | AI works using loaded memory as context |
| **3 — Capture** | "What new facts, decisions, or insights from this session should be saved to memory? Propose what to write." | AI proposes memory updates — writes nothing yet |
| **4 — Review** | "Show me exactly what you'll write before saving anything." | AI presents full file contents for approval |
| **5 — Close** | "Approved. Write confirmed items to _inbox/, update episode log, run lint pass, and close." | AI commits, runs lint, closes session |

See `procedural/session-loop.md` for the full workflow including autonomy level shortcuts.

---

## 10. New Project Initialisation Loop

Run this once when setting up any new vault. After init, switch to the standard session loop.

| Prompt | What To Say | What Happens |
|---|---|---|
| **Init 1** | "This is a new [project type]. Here is the context: [paste details]. Propose a topic map. Do not create files yet." | AI proposes topic map |
| **Init 2** | Approve or edit the topic map | Topic map confirmed |
| **Init 3** | "Generate initial semantic memory files based on the approved topic map. Write to _inbox/." | AI generates and stages files |
| **Init 4** | "Show me each file one by one for review." | AI presents files for approval/editing |
| **Init 5** | "All approved. Run lint pass, update index.md, confirm vault ready." | AI commits, vault is live |

See `procedural/new-project-init.md` for topic map examples for all 5 use cases.

---

## 11. The 5 Core Use Cases

### Use Cases Are Extensible

The 5 use cases below are the built-in starting point — not a fixed limit. The system is designed to grow as new project types are adopted. Use cases are living content defined in two files that must always stay in sync:

| File | What It Contains |
|---|---|
| `procedural/new-project-init.md` | Topic map examples for each use case — the primary definition |
| `SYSTEM-EXPLAINER.md` Section 11 | Plain language description of each use case — this section |

**To add a new use case**, use this prompt with the AI agent:

> "Read procedural/new-project-init.md and SYSTEM-EXPLAINER.md. I want to add a new use case called [name]. Here is the context: [describe it]. Propose a topic map, add it to new-project-init.md, update Section 11 in SYSTEM-EXPLAINER.md, increment the version in CLAUDE.md, and write all changes to _inbox/ for my review."

All use case edits follow the standard inbox-first and human review rules.

---

### Use Case 1 — Personal AI Assistant
**What it is:** A long-term memory layer for personal AI interactions across any model.
**What gets remembered:** User profile, preferences, communication style, ongoing personal projects, key relationships, life context.
**Key benefit:** You never re-explain yourself to any AI model again. Every session starts with full context.

### Use Case 2 — Software Development Project
**What it is:** A shared project memory for AI coding agents and human developers.
**What gets remembered:** Tech stack, architecture decisions, coding conventions, team members, past bugs and fixes, pending tasks.
**Key benefit:** Multiple AI coding agents share the same project memory without contradicting each other. Decisions are never re-litigated.

### Use Case 3 — Research & Knowledge Building
**What it is:** A compounding knowledge base that grows smarter with every session.
**What gets remembered:** Hypotheses, key sources, methodology, confirmed findings, open questions, session insights.
**Key benefit:** The episodic compression mechanism means every research session distills into permanent semantic knowledge automatically.

### Use Case 4 — Client & Business Management
**What it is:** A per-client memory vault for consultants, agencies, or account managers.
**What gets remembered:** Client profile, relationship history, active engagements, preferences, decision makers, commercial terms.
**Key benefit:** Any AI model picking up a client account instantly has full context. No onboarding time, no missed history.

### Use Case 5 — Content Creation & Writing
**What it is:** A persistent creative memory for writers, creators, and content teams.
**What gets remembered:** Voice and style guide, audience profile, topics already covered, content calendar, brand guidelines.
**Key benefit:** Long-running creative projects stay consistent across sessions and across AI models.

---

## 12. Memory Lifecycle

Every memory goes through a defined lifecycle:

```
NEW INFORMATION
      │
      ▼
┌──────────┐
│  _inbox/ │  status: pending
└────┬─────┘
     │ lint pass: promote / merge / reject
     ▼
┌──────────────┐
│ memory folder│  status: active
└────┬─────────┘
     │ TTL expires
     ▼
┌──────────────┐
│ stale check  │  status: stale
└────┬─────────┘
     ├── renewed → status: active (reset TTL)
     └── superseded → status: archived
                │
                ▼
         ┌──────────┐
         │ archive/ │  status: archived (never deleted)
         └──────────┘
```

### Episodic Compression

```
episodic/week-2026-04-15.md  (90+ days old)
      │ lint pass extracts key facts
      ▼
semantic/project-decisions.md  (updated with extracted facts)
      +
archive/week-2026-04-15.md    (original preserved)
```

---

## 13. The Lint Pass

The lint pass is the vault's health and lifecycle maintenance routine. It runs on a schedule and handles everything the system needs to stay accurate over time.

| Step | What It Does |
|---|---|
| TTL scan | Flags expired files as stale |
| Inbox promotion | Validates and promotes pending items |
| Conflict detection | Resolves contradictions in semantic memory |
| Episodic compression | Compresses old episodes to semantic facts |
| Orphan detection | Flags unlinked files |
| Prospective cleanup | Archives completed tasks |
| Index rebuild | Updates `_meta/index.md` |
| Log entry | Writes summary to `_meta/log.md` |

**Run frequency:** Daily for active systems. Weekly for low-activity vaults.

See `procedural/lint-pass.md` for the complete step-by-step workflow.

---

## 14. Working Memory Assembly

At the start of each session, the AI selectively loads vault files into its context window.

**Load order and limits:**

| Memory Type | What To Load | Max Files |
|---|---|---|
| _meta | CLAUDE.md + index.md | 2 (always) |
| Semantic | Topic-relevant files | 5–10 |
| Episodic | Most recent entries | 3–5 |
| Procedural | Session-relevant workflows | 1–3 |
| Prospective | Active tasks | All active |
| Retrieval | On-demand matches only | 3–5 |

**Do not load:** `archive/`, `_inbox/`, or the entire vault.

See `procedural/memory-assembly.md` for the complete workflow.

---

## 15. Agent Autonomy Levels

The system supports three levels of agent autonomy. Set the level in `_meta/index.md` per project.

| Level | Name | Agent Does Autonomously | Human Role |
|---|---|---|---|
| 1 | Assisted | Proposes all actions, writes nothing without approval | Approves every write |
| 2 | Semi-autonomous | Writes to _inbox/ freely, runs lint, flags conflicts | Reviews flagged items only |
| 3 | Autonomous | Full read/write/lint cycle | Reviews session summaries only |

**Default: Level 1.** Increase deliberately after trust is established.

**Future state:** Level 3 with a dedicated background agent is the target for the autonomous agent roadmap stage.

---

## 16. Multi-Model Operations

This vault is designed for environments where multiple AI models operate in the same vault.

### The Multi-Model Protocol

1. Every model registers on session start in `_meta/models.md`
2. Every model writes to `_inbox/` — never directly to memory folders
3. The lint pass is the only writer to active memory folders
4. Provenance is tracked via the `author:` field on every file
5. Write conflicts resolved by timestamp — newer `updated:` wins
6. Episodic logs are append-only — no model overwrites another's entry

### Recommended Roles In Multi-Model Systems

| Role | Model | Responsibility |
|---|---|---|
| Memory Manager | Claude Code | Lint pass, inbox management, index maintenance |
| Domain Expert | Any capable model | Reads memory, generates answers |
| Session Logger | Any model | Writes episodic logs after sessions |
| Curator | Human or trusted agent | Reviews conflicts, approves stale file decisions |

---

## 17. How To Use This As A Template

### Step 1 — Clone The Structure
Copy the entire vault folder to your new project location. Keep all `_meta/` and `procedural/` files. The `TEMPLATE.md` files in memory folders are guides — keep them as masters.

### Step 2 — Update CLAUDE.md
Update the project description, any custom TTL guidelines, and increment the version with a changelog entry.

### Step 3 — Run The Init Loop
Follow `procedural/new-project-init.md` to seed the vault with your project's topic-chunked semantic memory.

### Step 4 — Set Autonomy Level
Set the agent autonomy level in `_meta/index.md` appropriate for your project.

### Step 5 — Connect Claude Code
Point Claude Code at the vault folder path. Configure it to run the session loop at session start and the lint pass on your schedule.

### Step 6 — Register Models
Add all AI models to `_meta/models.md`.

### Step 7 — Run Sessions
Use `procedural/session-loop.md` for every session from here on.

---

## 17b. Chat Knowledge Extraction

### The Problem This Solves

New users arrive at the vault with months or years of valuable AI conversations already sitting on external platforms — ChatGPT, Perplexity, Gemini, Claude. Without extraction, all of that knowledge is lost when those chats are closed or forgotten. Existing users finish a valuable chat on an external platform and need a way to capture and import that knowledge without manually transcribing it.

The chat extraction workflow solves both problems.

### How It Works

The extraction happens at the source — inside the same AI chat where the conversation took place. You paste the extraction prompt at the end of any chat, and the AI reads its own conversation context to generate a structured markdown file ready for vault import.

```
External AI Chat (ChatGPT / Perplexity / Gemini / Claude)
        │
        │ 1. Paste CHAT-EXTRACTION-PROMPT.md at end of chat
        │
        ▼
AI performs pre-extraction self-check, secrets scan,
and hallucination guardrail check — then extracts
        │
        │ 2. AI outputs structured markdown extraction file
        │    with quality check table at the end
        │
        ▼
Review output — check quality check table for FAILs
        │
        │ 3. Save as extraction-YYYY-MM-DD-topic.md into _inbox/
        │
        ▼
Lint pass splits by section and promotes to correct folders
        │
        ▼
semantic/ state/ episodic/ procedural/ prospective/ retrieval/
```

### What Gets Extracted — v2.0.0 Full Section List

| Section | Memory Type | What It Contains |
|---|---|---|
| Summary | Episodic | 3-sentence overview |
| Primary Objective | Semantic | User goal, secondary objectives, constraints |
| Current Canonical State | STATE | Latest confirmed project state |
| Semantic Facts | Semantic | Durable confirmed facts with source attribution |
| State Memory | STATE | Current status, config, blockers — TTL: 30d |
| Episodic Narrative | Episodic | Conversation arc with temporal markers |
| Procedural Memory | Procedural | Workflows and processes defined |
| Retrieval Memory | Retrieval | Resources with access status and type |
| Prospective Memory | Prospective | Tasks with priority, deadline, assignee |
| Decisions Made | Semantic | With alternatives, reasoning, dependencies |
| Recommendations | Semantic | With adoption status and source |
| User Preferences | Semantic | Durable preferences — TTL: permanent |
| Named Entities | Semantic | People, companies, products, technologies |
| Alternatives Considered | Episodic | What was rejected and why |
| Corrections & Clarifications | Episodic | User corrections to AI statements |
| Open Questions | Prospective | Unresolved threads with priority |
| Assumptions & Inferences | Flagged | Never auto-promoted — human review required |
| Unverified Claims | Flagged | Pricing, benchmarks, availability claims |
| Constraints | Semantic | All constraints explicitly extracted |
| Contradictions | Flagged | Reversed positions — human review required |
| Failed Attempts | Episodic | Only where constraint was established |
| Sensitive Data | Redacted | Secrets detected and replaced with placeholders |
| Missing Context | Flagged | What would be needed to use this knowledge |
| Next Steps | Prospective | Prioritised action list |
| Next Session Primer | Working | Structured under 200 words — front-loads goal and blocker |
| AI Handoff | Working | Machine-readable yaml for agent consumption |
| Extraction Quality Check | Meta | 12-item pass/fail self-verification table |

### The 6th Storable Memory Type — STATE

The v2.0.0 extraction prompt introduces STATE as a distinct memory type:

- **Semantic** stores durable facts that remain true over time
- **STATE** stores current project status, active configuration, blockers, and decisions in force — things that are true now but will change

STATE items are stored in `semantic/` with TTL: 30d and tagged `[state, current]`. They are refreshed with each new extraction rather than kept permanently.

### Source Attribution System

Every extracted item carries one of four source labels:
- **USER** — explicitly stated by the user
- **AI** — generated or recommended by the AI model
- **EXTERNAL_SOURCE** — from a URL or document cited in the chat
- **INFERENCE** — inferred from context, never explicitly stated

This prevents AI recommendations from accidentally becoming stored facts — one of the most common failure modes in AI memory systems.

### Confidence And Status Ratings

**Confidence:**
- HIGH — directly stated and confirmed
- MEDIUM — reasonably inferred from context
- LOW — speculative or uncertain

**Status:**
- CONFIRMED / PROPOSED / ADOPTED / REJECTED / SUPERSEDED / UNKNOWN

### Stable Item IDs

Every important extracted item receives a stable ID (fact-001, decision-001, task-001). These IDs are preserved when items are promoted to memory folders, enabling automated deduplication, cross-referencing, and update tracking across future extraction sessions.

### Secrets And PII Protection

The extraction prompt includes a mandatory secrets scan before any extraction begins. Passwords, API keys, tokens, credentials, and PII are replaced with typed placeholders like `[SECRET_REDACTED — type: API_KEY]`. No extraction file containing unredacted secrets is promoted by the lint pass.

### Extraction Quality Check

Every extraction ends with a 12-item pass/fail self-verification table. The AI checks its own output for hallucination, secret leakage, duplicate entries, misattributed decisions, missing TTL values, and primer length compliance before finalising. Any FAIL item must be reviewed before vault import.

### The Next Session Primer

Every extraction file includes a structured Next Session Primer capped at 200 words. It front-loads the current goal and biggest blocker so it works even in a small context window. It includes a continuation instruction telling future AI agents not to reopen already-settled decisions.

### The AI Handoff Block

Every extraction file ends with a machine-readable yaml block — the AI HANDOFF. This is the primary section consumed by automated AI agents when picking up a project. It contains objective, current state, adopted decisions, constraints, blockers, next action, and a do-not-reopen list.

### When To Use It

- **New vault setup** — extract your most valuable existing chats to seed initial memory
- **After any external chat** — capture knowledge from any platform
- **Project handoff** — extract before switching AI models or platforms
- **Knowledge archiving** — preserve important conversations before they are lost
- **Mid-project context reset** — run extraction to produce a clean canonical state snapshot

See `CHAT-EXTRACTION-PROMPT.md` for the full prompt and step-by-step instructions.

---

## 18. Future Roadmap

The following capabilities are designed for but not yet implemented. Preserve compatibility with these future states when building on this system.

### Stage 1 — Current: Human-In-The-Loop
Manual 5-prompt session loop. Human approves every write. AI assists. ✅ Built.

### Stage 2 — Semi-Autonomous Agent
Claude Code runs session loop, lint pass, and inbox promotion automatically. Human reviews flagged items only. Achievable now with Claude Code + scheduler.

### Stage 3 — Fully Autonomous Memory Agent
Dedicated agent monitors the vault, runs lint on schedule, compresses episodic memory, refreshes retrieval documents, and alerts human only when intervention is needed.

### Stage 4 — Multi-Agent Orchestration
Specialist agents (research, coding, writing, communication) sharing one vault via a Memory Manager agent controller. The vault is the shared brain.

### Stage 5 — SaaS / App
- Hosted vault per user or team
- Web/mobile interface for viewing and approving memories
- Model-agnostic API plugging into any AI provider
- One-click project templates for the 5 core use cases
- The killer differentiator: model-agnostic design — works with any AI, now and future

---

## 19. Design Principles & Philosophy

**Files Are The Interface.** No proprietary format, no database schema, no API. A plain markdown file is readable by any AI model, any human, any tool. The system survives tool changes, model changes, and platform changes.

**Compounding Knowledge.** Inspired by Karpathy's llm-wiki: every session leaves the vault smarter. Episodes compress into semantic facts. Good answers become new memory files. The vault compounds over time.

**Stage Before Committing.** Nothing enters active memory without going through `_inbox/` and the lint pass. This prevents memory pollution — the gradual accumulation of false, duplicate, or contradictory facts.

**Memory Has A Lifecycle.** Facts change. Every memory that can become stale gets a TTL. The lint pass enforces it. The archive preserves history. Stale facts don't pollute active memory.

**Topic Chunks, Not Files Per Fact.** Token efficiency and selective loading are balanced through topic-chunked files. Group what belongs together. Separate what would ever be needed independently.

**Templates Are Masters.** Duplicate and rename. Never edit the master. This one rule prevents a class of errors that corrupt vault structure over time.

**Procedural Memory Is Sacred.** Workflows and rules are never auto-modified at runtime. They are version-controlled and treated with the same care as code.

**Selective Loading Over Bulk Loading.** More context is not better. Irrelevant context degrades reasoning quality. Load relevantly, not exhaustively.

**Provenance Everywhere.** Every memory file knows who created it, when, and from where. In multi-model environments, this makes conflict resolution and trust calibration possible.

**Simple Enough For Anyone.** The 5-prompt session loop is the UX layer. A non-technical user should be able to run a full session without understanding the underlying architecture.

---

## 20. Known Limitations

**No native vector search.** Retrieval is tag/keyword-based. For large vaults (500+ files), this may miss relevant memories. Mitigation: add a vector index layer on top.

**Lint pass requires triggering.** Lifecycle management doesn't run itself. In low-activity systems, stale memories accumulate if lint passes are skipped.

**Context window limits.** A very large vault can't be fully loaded. Selective loading helps, but very long-running projects may need periodic vault pruning.

**No real-time sync.** In multi-model environments, simultaneous writes to `_inbox/` may create duplicates. The lint pass resolves this, but there's a window between writes and lint.

**Procedural memory drift.** Workflows not regularly reviewed can become outdated. Schedule deliberate procedural memory reviews.

---

## 21. Glossary

| Term | Definition |
|---|---|
| **Vault** | The root folder containing all memory files |
| **Working memory** | The active context window; assembled per session, never stored |
| **Semantic memory** | Durable facts stored in `semantic/` as topic-chunked files |
| **Episodic memory** | Time-stamped event logs in `episodic/` |
| **Procedural memory** | Workflows and rules in `procedural/` |
| **Retrieval memory** | External reference docs in `retrieval/` |
| **Prospective memory** | Future tasks and intentions in `prospective/` |
| **Parametric memory** | Knowledge frozen in model weights; not stored in vault |
| **Lint pass** | The scheduled health and lifecycle maintenance routine |
| **TTL** | Time-to-live; period after which a memory should be reviewed or expired |
| **Frontmatter** | YAML metadata block at the top of every vault file |
| **Topic chunk** | A file grouping related facts that would always be needed together |
| **Topic map** | The approved list of filenames and descriptions for a project vault |
| **Episodic compression** | Summarising old episode logs into semantic memory files |
| **Inbox staging** | The `_inbox/` folder where all new memories land before lint promotion |
| **Provenance** | The origin and authorship trail of a memory file |
| **Compounding** | The process by which each session enriches the vault cumulatively |
| **Memory Manager** | The controller (Claude Code or agent) that governs vault reads/writes |
| **Autonomy level** | The degree to which the agent operates without human approval |
| **Template duplication** | Duplicating TEMPLATE.md and renaming before filling in — never edit the master |
| **Session loop** | The standard 5-prompt sequence run every session |
| **Init loop** | The 5-prompt sequence run once to set up a new project vault |

---

*End of System Documentation — v2.0.0*

*For the rules engine and operational schema, see `_meta/CLAUDE.md`.*
*For the vault content catalog, see `_meta/index.md`.*
*For the action history, see `_meta/log.md`.*
*For the session loop, see `procedural/session-loop.md`.*
*For new project setup, see `procedural/new-project-init.md`.*
