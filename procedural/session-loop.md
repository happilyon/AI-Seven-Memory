---
id: proc-session-loop
type: procedural
title: Standard Session Prompting Loop — V2
created: 2026-10-08
updated: 2026-10-08
author: system
confidence: high
ttl: permanent
status: active
tags: [session, prompting, loop, core-ux, v2]
related: [proc-memory-assembly, proc-lint-pass, proc-inbox-promotion, proc-new-project-init]
source: system
---

# Workflow: Standard Session Prompting Loop — V2

## Purpose

The core UX of the system. Every session follows this 5-prompt sequence. V2 updates Prompts 3 and 4 to use the tiered promotion system — eliminating approval fatigue by only surfacing items that genuinely need human judgment.

## When To Use This

Every ongoing session after vault initialisation. For new projects run `procedural/new-project-init.md` first.

---

## The 5-Prompt Loop

### Prompt 1 — Start Session

```
Start a new session. Read CLAUDE.md and index.md, assemble working memory
using relevance scoring, and give me a context summary covering: current
project status, key facts loaded, last session date, active tasks, inbox
status, and vault health rating.
```

**What the AI does:**
- Loads CLAUDE.md and index.md
- Identifies session topic
- Scores and loads relevant semantic, episodic, procedural, and prospective files
- Checks for continuity snapshot from interrupted sessions
- Registers session in models.md
- Outputs context summary

**What you do:**
- Read the summary — correct anything wrong before proceeding
- If a continuity snapshot is found — decide to resume or start fresh

---

### Prompt 2 — Work

No special prompt. Normal conversation, questions, tasks, or project work.

**What the AI does:**
- Works using assembled working memory as context
- References existing memories naturally
- Flags contradictions with existing memory as they arise

---

### Prompt 3 — Capture

```
What should be saved from this session? Apply the tiered promotion system:
tell me what will auto-promote (Tier 1), what needs my approval (Tier 2),
and what goes to batch review (Tier 3). Show me only the Tier 2 items.
```

**What the AI does:**
- Reviews the full session
- Classifies every candidate memory item by tier
- Tier 1 items: listed as "will auto-promote — no action needed"
- Tier 2 items: presented individually for human decision
- Tier 3 items: listed as "going to batch review — no action needed this session"

**What you do:**
- Glance at the Tier 1 list — confirm it looks right
- Review each Tier 2 item and respond: APPROVE / EDIT / REJECT / DEFER
- No action needed on Tier 3 items

---

### Prompt 4 — Review

```
Show me the full content of each Tier 2 approved item before writing.
Include confidence rating and source type on each one.
```

**What the AI does:**
- Shows complete content only for Tier 2 approved items
- Does not show Tier 1 items — they auto-promote
- Waits for final confirmation before writing anything

**What you do:**
- Review each Tier 2 file
- Edit inline if needed
- Confirm when ready

---

### Prompt 5 — Close Session

```
Confirmed. Write Tier 2 approved items to _inbox/, auto-promote Tier 1
items, move Tier 3 items to _inbox/review/, update today's episode log,
run the lint pass with checkpoints, and close with a summary.
```

**What the AI does:**
- Writes Tier 2 approved items to `_inbox/`
- Auto-promotes Tier 1 items directly to memory folders
- Moves Tier 3 items to `_inbox/review/`
- Creates episodic log entry in `_inbox/`
- Runs lint pass (all 11 checkpoints)
- Updates models.md and logs SESSION_END
- Outputs closing summary

**What you do:**
- Read the closing summary
- Note any compression candidates or Tier 2 items still pending

---

## Quick Reference Card

| Prompt | Say | Purpose |
|---|---|---|
| 1 | "Start session, load memory, give context summary" | Load context |
| 2 | *(normal work)* | Do the work |
| 3 | "What to save? Show me Tier 2 only" | Tiered capture |
| 4 | "Show Tier 2 full content before writing" | Review before save |
| 5 | "Confirmed — write, promote, lint, close" | Commit and close |

---

## Optional Maintenance Prompts

Use these as needed — not every session.

### Resume Interrupted Session
```
Check _inbox/ for a continuity snapshot from a previous interrupted session.
If found, load it and tell me where we left off and what the next action was.
```

### Vault Health Check
```
Run a vault health check without a full lint pass. Show me: inbox count,
review pile count, stale file count, overdue tasks, last lint date,
and overall health rating (Green / Amber / Red).
```

### Renew Stale Files
```
Show me all files with status: stale that are within the 14-day renewal
window. For each one: filename, what it contains, when it expired.
I will renew or archive each one.
```

### Weekly Batch Review
```
Show me all items in _inbox/review/. For each one: filename, one-line
summary, confidence, source type, and recommended action.
I will approve, edit, or reject each one.
```

### Manual Lint Pass
```
Run a full lint pass now. Execute all 11 checkpoints in order. Report
each checkpoint result and stop if any step fails.
```

---

## Autonomy Level Adjustments

**Level 1 (Assisted — default):** Full 5-prompt loop as above. Human reviews Tier 2 only.

**Level 2 (Semi-autonomous):** Skip Prompts 3 and 4. End with:
```
Save Tier 1 items automatically, queue Tier 2 to inbox, move Tier 3 to
review pile, run lint pass, and send me a summary.
```

**Level 3 (Autonomous):** Agent runs full loop. Human receives session summary notification only.

---

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-10-08 | system | Initial version |
| 2.0 | 2026-10-08 | system | V2 — tiered promotion in Prompts 3 and 4, context summary in Prompt 1, optional maintenance prompts added, resume interrupted session prompt, vault health check prompt, weekly batch review prompt |
