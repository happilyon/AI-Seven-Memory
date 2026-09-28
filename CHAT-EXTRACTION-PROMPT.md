# AI Chat Knowledge Extraction Prompt

> **Version:** 1.0.0 | **Created:** 2026-09-15 | **Updated:** 2026-09-15
>
> Copy the prompt below and paste it at the end of any AI chat on any platform —
> ChatGPT, Perplexity, Claude, Gemini, or any other. The AI will analyse its own
> conversation context and output a single structured markdown file ready to drop
> into your memory vault _inbox/ folder.
>
> Do not modify the prompt structure. Fill in only the fields marked [in brackets].

---

## THE EXTRACTION PROMPT
### Copy everything below this line and paste it into your AI chat

---

I need you to analyse our entire conversation above and extract all valuable
knowledge into a single structured markdown document.

Before you begin, note the following:

- Your name and version: [e.g. ChatGPT-4o / Claude Sonnet / Perplexity / Gemini]
- Today's date: [YYYY-MM-DD]
- Chat topic or project name: [brief description]

---

### PRE-EXTRACTION SELF-CHECK

Before writing anything, silently perform these checks:

1. **Context completeness** — Can you access the entire conversation?
   If not, state exactly what portion is visible. Do not claim full analysis
   if you can only see partial context.

2. **Conversation length estimate** — If the chat exceeds your context limit,
   summarise the earliest turns into 3-5 bullet points, then extract from
   the full recent context. Do not drop SUMMARY or DECISIONS sections due to length.

3. **Secrets scan** — Does the conversation contain passwords, API keys,
   tokens, credentials, private URLs, .env values, or SSH keys?
   If yes, redact them and replace with `[SECRET_REDACTED — type: API_KEY]`
   or similar. Never reproduce secrets in the extraction output.

4. **Hallucination check** — Commit to this rule before starting:
   Never fill gaps with assumptions, general knowledge, or information
   from outside this conversation unless explicitly marked as INFERENCE.
   If a section has no valid entries, write "None identified."
   Do not invent content to fill the template.

5. **Length management** — If the chat is long (over 50 turns), prioritise
   extraction in this order: (1) Decisions, (2) Action items,
   (3) Corrected facts, (4) Resources, (5) Everything else.
   Keep procedural steps verbatim rather than summarising them.

6. **Output length** — If the platform has output length limits, split the
   markdown logically at section boundaries and label each part clearly
   as "Part 1 of N / Section: [name]" so parts can be reassembled.

---

### SOURCE ATTRIBUTION RULES

Every extracted item must be attributed to one of these four sources:

- **USER** — explicitly stated by the user
- **AI** — generated, recommended, or suggested by the AI
- **EXTERNAL_SOURCE** — from a URL, document, or reference cited in the chat
- **INFERENCE** — reasonably inferred from context but never explicitly stated

**Critical rules:**
- Never elevate an AI recommendation to a stored fact
- Never treat INFERENCE as CONFIRMED
- Never present a search result as independently verified fact
- Distinguish between DISCOVERED, VERIFIED, RECOMMENDED, and USER-PROVIDED
  for any external research or web browsing that occurred

---

### WHAT TO INCLUDE

Extract only high-value content:
- Facts, knowledge, and insights that were established or confirmed
- Decisions that were made and the reasoning behind them
- Recommendations given and the context for each
- Workflows, processes, or steps that were defined
- Resources, tools, references, or sources mentioned
- Tasks, actions, or follow-up items identified
- Open questions that were raised but not fully resolved
- User preferences that were clearly and durably expressed
- Named entities — people, companies, products, software, models,
  projects, domains, technologies, URLs
- File paths, commands, package names, versions, ports, environment names
  (for technical chats)
- Failed attempts IF the failure itself establishes a boundary condition
  or negative constraint (e.g. "X approach does not work because...")
- User corrections and clarifications made during the chat

---

### WHAT TO EXCLUDE

Do not include:
- Greetings, pleasantries, and filler exchanges
- Failed attempts that produced no useful constraint or learning
- Repeated questions or re-explanations of the same point
- Tangential discussions that did not contribute to the core topic
- Content that was explicitly dismissed or overruled during the chat
- Secrets, credentials, API keys, tokens, passwords — redact these
- Information so ephemeral it would not be useful 30-90 days from now
- Large code blocks — extract what the code does, why it matters,
  key parameters, and final working version only

---

### DEDUPLICATION RULES

- Do not create separate entries for the same knowledge merely because
  it was discussed multiple times
- Consolidate repeated information into one canonical entry
- When multiple versions of a fact, decision, or configuration exist,
  retain the latest confirmed state and mark previous versions as SUPERSEDED
- When importing to vault: if a fact duplicates existing vault knowledge,
  flag it as DUPLICATE_CANDIDATE rather than skipping it, so the lint
  pass can merge or update confidence

---

### CONFIDENCE AND STATUS RATINGS

**Confidence — rate each extracted item:**
- **HIGH** — directly stated and confirmed by the user; explicitly decided
- **MEDIUM** — reasonably inferred from context; partially discussed
- **LOW** — speculative; user said "maybe", "possibly", or "I think"

**Status — assign to facts and decisions:**
- **CONFIRMED** — explicitly agreed upon
- **PROPOSED** — suggested but not yet accepted
- **ADOPTED** — accepted and being acted upon
- **REJECTED** — explicitly ruled out
- **SUPERSEDED** — replaced by a newer decision or fact
- **UNKNOWN** — status unclear from the conversation

**Critical rule:** Never promote MEDIUM or LOW confidence items into
durable memory as if they were confirmed facts.

---

### TTL ASSIGNMENT BY CONTENT TYPE

Assign TTL based on content type, not a fixed value:
- Ephemeral tasks and immediate action items → `7d`
- Project status and current blockers → `30d`
- Project workflows and processes → `90d`
- Durable facts and technical decisions → `365d`
- Core user preferences and constraints → `permanent`
- Items needing human review → `review`

---

### HOW TO CLASSIFY EACH PIECE OF KNOWLEDGE

Sort every extracted item into one of these memory types:

- **SEMANTIC** — durable facts, preferences, named entities, world knowledge
- **EPISODIC** — what happened in this chat, decisions made, outcomes reached
- **PROCEDURAL** — workflows, step-by-step processes, rules, how-to knowledge
- **RETRIEVAL** — external documents, tools, resources, references mentioned
- **PROSPECTIVE** — tasks, action items, follow-ups, things that need to happen
- **STATE** — current project status, active configuration, current blockers,
  current technology stack, current decisions in force. This is distinct from
  durable semantic memory — state is temporary and will change.

---

### OUTPUT FORMAT

Generate the extraction as a single markdown file using exactly this structure.
Preserve the user's exact names for projects, systems, products, workflows,
files, folders, and architecture components throughout.

---

```markdown
---
id: extraction-[YYYY-MM-DD]-[chat-topic-slug]
type: extraction
title: Knowledge Extraction — [Chat Topic] — [YYYY-MM-DD]
created: [YYYY-MM-DD]
updated: [YYYY-MM-DD]
author: [your AI model name and version]
confidence: high
ttl: review
status: pending
tags: [extraction, chat-import, add-relevant-tags]
related: []
source: [platform name] — [chat topic or session description]
project: [project name or NONE]
project_status: [active / paused / complete / unknown]
domain: [topic domain]
environment: [development / staging / production / N/A]
context_completeness: [complete / partial / unknown]
---

# Knowledge Extraction — [Chat Topic] — [YYYY-MM-DD]

**Source Platform:** [ChatGPT / Perplexity / Claude / Gemini / other]
**AI Model:** [model name and version]
**Extraction Date:** [YYYY-MM-DD]
**Chat Length:** [short <20 turns / medium 20-50 turns / long >50 turns]
**Context Access:** [complete / partial — describe what was visible]
**Secrets Detected:** [YES — redacted / NO]

---

## SUMMARY

[3 sentences maximum. What was this chat about, what was the core
problem or goal, and what was the overall outcome or conclusion reached.
Human-readable.]

---

## PRIMARY OBJECTIVE

**What the user was trying to accomplish:**
- PRIMARY_OBJECTIVE: [main goal]
- SECONDARY_OBJECTIVES: [other goals, if any]
- CONSTRAINTS: [budget, hardware, software, time, platform, compatibility,
  business, user preferences — list all constraints explicitly]

---

## CURRENT CANONICAL STATE

[The latest confirmed state of the project or topic as of the end of this chat.
This is the single most important section for feeding future AI sessions.
Include only the latest confirmed state — not historical states.]

- Current status:
- Current technology/approach:
- Current configuration:
- Current blockers:
- Last known working state: [for technical projects]
- Decisions currently in force:

---

## KEY KNOWLEDGE EXTRACTED

### Semantic Memory — Durable Facts
[One bullet per fact. Never include speculative or unconfirmed items here.]
Format: `FACT: [the fact] | Source: USER/AI/EXTERNAL_SOURCE/INFERENCE | Confidence: HIGH/MEDIUM/LOW | TTL: [value] | ID: fact-[NNN]`

- FACT: | Source: | Confidence: | TTL: | ID: fact-001

### State Memory — Current Project State
[Temporary state that will change — distinct from durable facts.]
Format: `STATE: [current state item] | As of: [YYYY-MM-DD] | TTL: 30d`

- STATE: | As of: | TTL:

### Episodic Memory — What Happened In This Chat
[Narrative summary of the conversation arc with temporal markers.
Note when major shifts occurred: "Early in chat we tried X; after Y at
turn ~N, we pivoted to Z." 3-5 sentences.]

### Procedural Memory — Workflows & Processes Defined
Format: `PROCESS: [name] — [brief description of steps] | Source: USER/AI | Confidence: HIGH/MEDIUM/LOW | ID: proc-[NNN]`

- PROCESS: | Source: | Confidence: | ID: proc-001

### Retrieval Memory — Resources & References
Format: `RESOURCE: [name or exact URL] — [why mentioned] | Type: OFFICIAL_DOCS/ARTICLE/REPO/TOOL/SERVICE | Access: ACCESSED/NOT_ACCESSED | Source: USER/AI/EXTERNAL_SOURCE`

- RESOURCE: | Type: | Access: | Source:

### Prospective Memory — Tasks & Action Items
Format: `TASK: [what needs to happen] | Priority: HIGH/MEDIUM/LOW | Deadline: [date or OPEN] | Assigned: USER/AI/BOTH | TTL: [value] | ID: task-[NNN]`

- TASK: | Priority: | Deadline: | Assigned: | TTL: | ID: task-001

---

## DECISIONS MADE

[Every confirmed decision reached during the chat.]
Format:
```
DECISION: [what was decided]
ID: decision-[NNN]
Source: USER/AI/MUTUAL
Status: CONFIRMED/ADOPTED/PROPOSED
Alternatives considered: [what was rejected and why]
Reasoning: [why this was chosen]
Depends on: [assumption or constraint this decision relies on]
Confidence: HIGH/MEDIUM/LOW
TTL: [value]
```

---

## RECOMMENDATIONS

[Every recommendation made — by either party.]
Format: `RECOMMENDATION: [what] | By: USER/AI | Context: [why] | Status: ADOPTED/PENDING/REJECTED | Confidence: HIGH/MEDIUM/LOW`

- RECOMMENDATION: | By: | Context: | Status: | Confidence:

---

## USER PREFERENCES DETECTED

[Durable preferences clearly expressed during the chat — only classify as
durable when the conversation strongly supports that conclusion.]
Format: `PREFERENCE: [preference] | Type: TOOL/WORKFLOW/STYLE/TECHNOLOGY/CONSTRAINT | Confidence: HIGH/MEDIUM/LOW | TTL: permanent`

- PREFERENCE: | Type: | Confidence: | TTL:

---

## NAMED ENTITIES

[Important entities mentioned — extract separately for knowledge graph linking.]

| ID | Entity | Type | Notes |
|---|---|---|---|
| entity-001 | [name] | PERSON/COMPANY/PRODUCT/SOFTWARE/MODEL/PROJECT/DOMAIN/TECHNOLOGY/URL | [brief context] |

---

## ALTERNATIVES CONSIDERED

[Significant alternatives that were raised and rejected.]
Format: `ALTERNATIVE: [what was considered] | Rejected because: [reason] | Decision made instead: decision-[NNN]`

- ALTERNATIVE: | Rejected because: | Decision made instead:

---

## CORRECTIONS & CLARIFICATIONS

[Every instance where the user corrected the AI or refined a previous statement.]
Format: `CORRECTION: [original claim] → [corrected understanding] | Source: USER | Confidence: HIGH`

- CORRECTION: → | Confidence:

---

## OPEN QUESTIONS

[Questions raised but not fully resolved.]
Format: `QUESTION: [the unresolved question] | Priority: HIGH/MEDIUM/LOW | TTL: [value] | ID: question-[NNN]`

- QUESTION: | Priority: | TTL: | ID: question-001

---

## ASSUMPTIONS & INFERENCES

[Things the AI inferred but that were never explicitly confirmed by the user.
These must never be promoted to durable memory without human verification.]
Format: `INFERENCE: [what was inferred] | Based on: [reasoning] | Confidence: LOW/MEDIUM | Requires confirmation: YES`

- INFERENCE: | Based on: | Confidence: | Requires confirmation: YES

---

## UNVERIFIED CLAIMS

[Statements about pricing, compatibility, benchmarks, capabilities,
availability, or future plans discussed without confirmation.]
Format: `UNVERIFIED: [the claim] | Type: PRICING/COMPATIBILITY/BENCHMARK/CAPABILITY/AVAILABILITY/FUTURE_PLAN | Source: USER/AI`

- UNVERIFIED: | Type: | Source:

---

## CONSTRAINTS

[All constraints identified — budget, hardware, software, time,
compatibility, platform, technical, business, user preferences.]
Format: `CONSTRAINT: [description] | Type: BUDGET/HARDWARE/SOFTWARE/TIME/COMPATIBILITY/PLATFORM/TECHNICAL/BUSINESS | Confirmed: YES/NO`

- CONSTRAINT: | Type: | Confirmed:

---

## CONTRADICTIONS & SUPERSEDED INFORMATION

[Points where the conversation changed direction or reversed a position.
Include conflicting specs, dates, versions, preferences, or decisions.
Mark which statement is the latest confirmed state.]
Format: `CONTRADICTION: [topic] | Original: [first position] | Revised: [final position] | Latest confirmed: [which is current] | Status: SUPERSEDED`

- CONTRADICTION: | Original: | Revised: | Latest confirmed: | Status:

---

## FAILED ATTEMPTS WITH LEARNING VALUE

[Failed approaches that established a boundary condition or constraint.]
Format: `FAILED_ATTEMPT: [what was tried] | Why it failed: [reason] | Constraint established: [what we now know not to do]`

- FAILED_ATTEMPT: | Why it failed: | Constraint established:

---

## SENSITIVE DATA DETECTED

[Secrets, credentials, or PII found and redacted.]
Format: `REDACTED: [SECRET_REDACTED — type: API_KEY/PASSWORD/TOKEN/PII/OTHER] | Location: [approximate — e.g. "mentioned when setting up X"]`

[If none: write "None detected."]

---

## MISSING CONTEXT

[Information that would be needed to confidently use this extracted
knowledge in a future session but was not available in this chat.]
Format: `MISSING: [what is unknown] | Why it matters: [impact on using this knowledge]`

- MISSING: | Why it matters:

---

## WHAT WAS EXCLUDED AND WHY

[Brief note on what was filtered out — failed attempts, rejected ideas,
tangential discussions, ephemeral content. 2-3 sentences maximum.]

---

## VAULT IMPORT NOTES

[Instructions for the vault AI agent on how to process this extraction.]

Promote by memory type:
- Semantic facts (HIGH confidence only) → `semantic/`
- State memory → `semantic/` with TTL: 30d
- Episodic narrative → `episodic/`
- Workflows and processes → `procedural/`
- Resources and references → `retrieval/`
- Tasks and open questions → `prospective/`
- User preferences → `semantic/` with TTL: permanent

Flag for human review before promotion:
- All MEDIUM and LOW confidence items
- All INFERENCE items
- All UNVERIFIED CLAIMS
- All CONTRADICTION items
- All DUPLICATE_CANDIDATE items
- Any item where Source: AI (verify before treating as fact)

Do not promote:
- ASSUMPTIONS & INFERENCES without human confirmation
- UNVERIFIED CLAIMS as facts
- AI recommendations as user decisions

---

## NEXT STEPS

[Prioritised list — highest priority first.]
1. [Most important immediate next action]
2.
3.
4.
5.

---

## NEXT SESSION PRIMER

[A structured context block for pasting at the start of any future AI session.
Must be under 200 words. Front-load the current goal and biggest blocker.
Designed to work even in a small context window.]

**Objective:** [what we are trying to achieve]
**Current state:** [where things stand right now]
**Key decisions in force:** [most important adopted decisions — do not reopen these]
**Constraints:** [most important constraints]
**Outstanding work:** [what still needs to happen]
**Immediate next action:** [single most important next step]

Continuation instruction: If continuing this project, start from the decisions
and constraints above rather than reconsidering already settled decisions unless
new evidence, changed requirements, or a new constraint requires it.
Treat ADOPTED decisions as the current baseline.

[Optionally: 3-5 sentence narrative version of the above for human-readable priming]

---

## AI HANDOFF

[Machine-readable canonical representation for AI agent consumption.
This is the primary section for automated vault processing and agent handoff.]

```yaml
objective: [primary goal]
current_state: [latest confirmed state]
decisions:
  - id: decision-001
    summary: [brief]
    status: ADOPTED
constraints:
  - [constraint 1]
  - [constraint 2]
blockers:
  - [blocker 1]
next_action: [single most important next step]
do_not_reopen:
  - [settled decision 1]
  - [settled decision 2]
key_entities:
  - [entity-001: name]
```

---

## EXTRACTION QUALITY CHECK

[Internal verification — the AI must check each item before finalising output.]

| Check | Result |
|---|---|
| Did I invent anything not in the conversation? | PASS / FAIL |
| Did I confuse AI recommendation with user decision? | PASS / FAIL |
| Did I preserve the latest confirmed state? | PASS / FAIL |
| Did I identify all contradictions? | PASS / FAIL |
| Did I omit unresolved questions? | PASS / FAIL |
| Did I accidentally include secrets or PII? | PASS / FAIL |
| Did I duplicate information unnecessarily? | PASS / FAIL |
| Did I mark all AI-sourced items correctly? | PASS / FAIL |
| Did I assign TTL values to all items? | PASS / FAIL |
| Did I stay under 200 words in Next Session Primer? | PASS / FAIL |
| Context completeness declared? | PASS / FAIL |
| Overall extraction quality: | PASS / FAIL |

---

*Extraction generated by [AI model name] on [YYYY-MM-DD]*
*Source: [platform] | Schema: Universal AI Memory Vault v2.2.0*
*Extraction prompt version: 2.0.0*
```

---

### AFTER THE AI GENERATES THE OUTPUT

1. Review the EXTRACTION QUALITY CHECK table — any FAIL items need attention
2. Review all MEDIUM and LOW confidence items before vault import
3. Check SENSITIVE DATA DETECTED — confirm all secrets are redacted
4. Edit anything wrong, missing, or needing clarification
5. Save the output as a `.md` file named: `extraction-YYYY-MM-DD-topic.md`
6. Drop it into your vault `_inbox/` folder in Obsidian
7. Run the lint pass — it will split the extraction by memory type and
   promote each section to the correct memory folder
8. Use the **AI HANDOFF** section when passing context to an AI agent
9. Use the **NEXT SESSION PRIMER** when starting a new human-facing chat

---

## CHANGELOG

| Version | Date | Change |
|---|---|---|
| 1.0.0 | 2026-09-15 | Initial release — full extraction prompt with source attribution, STATE memory, secrets scanning, quality check, AI handoff block |

---

*End of Extraction Prompt — v2.0.0*
*For vault import instructions see `procedural/chat-extraction.md`*
*For the lint pass workflow see `procedural/lint-pass.md`*
