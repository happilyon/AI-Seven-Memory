---
id: proc-session-loop
type: procedural
title: Standard Session Prompting Loop
created: 2026-09-15
updated: 2026-09-15
author: system
confidence: high
ttl: permanent
status: active
tags: [session, prompting, loop, core-ux]
related: [proc-memory-assembly, proc-lint-pass, proc-inbox-promotion, proc-new-project-init]
source: system
---

# Workflow: Standard Session Prompting Loop

## Purpose

This is the core UX of the entire system. Every session — regardless of project type, user, or AI model — follows this same 5-prompt sequence. It is designed to be simple enough for a non-technical user to run from day one.

## When To Use This

Use this loop for every ongoing session after the vault has been initialised. For brand new projects, run `procedural/new-project-init.md` first, then use this loop from session 2 onwards.

---

## The 5-Prompt Loop

### Prompt 1 — Start Session
Copy and send this exactly:

> "Start a new session. Read CLAUDE.md and index.md, assemble my working memory, and summarise what you know about this project so far."

**What the AI does:**
- Loads `_meta/CLAUDE.md` and `_meta/index.md`
- Identifies and loads relevant semantic, episodic, procedural, and prospective files
- Registers the session in `_meta/models.md`
- Outputs a summary of current project knowledge so you can confirm context is correct

**What you do:**
- Read the summary
- Correct anything wrong or outdated before proceeding
- If the summary looks right, move to Prompt 2

---

### Prompt 2 — Work
No special prompt. Just have your normal conversation, ask questions, give tasks, or work on your project.

**What the AI does:**
- Works using the assembled working memory as context
- References existing memories naturally in its responses
- Flags if it encounters something that contradicts existing memory

**What you do:**
- Work normally
- Note anything that feels like it should be remembered for future sessions

---

### Prompt 3 — Capture
Copy and send this at the end of your working conversation:

> "What new facts, decisions, or insights from this session should be saved to memory? Propose what to write and which files to update or create, following the topic-chunking rules."

**What the AI does:**
- Reviews the full session
- Identifies new facts → proposes additions to existing semantic files or new files
- Identifies decisions made → proposes episodic log entry
- Identifies new tasks → proposes prospective entries
- Identifies outdated memories → flags for update or archive
- Presents a structured proposal, writes nothing yet

**What you do:**
- Review the proposal
- Approve, edit, or remove individual items
- Move to Prompt 4

---

### Prompt 4 — Review
Copy and send this:

> "Show me exactly what you're about to write before saving anything. Present each file's full content for my approval."

**What the AI does:**
- Shows the complete content of every file it intends to write
- Waits for explicit approval on each item
- Does not write anything until approved

**What you do:**
- Read each proposed file carefully
- Edit inline if needed
- Approve each item or mark it rejected
- When all items are resolved, move to Prompt 5

---

### Prompt 5 — Close Session
Copy and send this after approving items in Prompt 4:

> "Approved. Write the confirmed items to _inbox/, update the episode log for today's session, run the lint pass, and close this session with a summary of what was saved."

**What the AI does:**
- Writes all approved items to `_inbox/` with `status: pending`
- Creates or updates today's episodic log file in `_inbox/`
- Runs the lint pass (promotes inbox items, checks conflicts, updates index)
- Updates `_meta/models.md` session entry with end time
- Logs `SESSION_END` in `_meta/log.md`
- Outputs a closing summary: what was saved, what was archived, what's pending

**What you do:**
- Read the closing summary
- Flag anything that looks wrong
- Close the session

---

## Quick Reference Card

| Prompt | Say | Purpose |
|---|---|---|
| 1 | "Start a new session..." | Load memory, confirm context |
| 2 | *(normal work)* | Do the actual work |
| 3 | "What should be saved..." | Identify new memories |
| 4 | "Show me what you'll write..." | Review before saving |
| 5 | "Approved. Write and close..." | Commit and close |

---

## Shortcuts For Experienced Users

Once you're comfortable with the loop, these shortened prompts work the same way:

- **Start:** "New session, load memory."
- **Capture:** "What should we save from this session?"
- **Review:** "Show me before you write."
- **Close:** "Approved, write and close."

---

## Autonomy Level Adjustments

The loop above assumes **Level 1 (Assisted)** — human approves every write.

**Level 2 (Semi-autonomous):** Skip Prompts 3 and 4. End with:
> "Save anything worth keeping from this session to _inbox/ and run the lint pass."

**Level 3 (Autonomous):** The agent runs the full loop without prompting. You receive a session summary notification only.

See `_meta/CLAUDE.md` → Agent Autonomy Levels for how to set the level for your project.

---

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-09-15 | system | Initial release |
