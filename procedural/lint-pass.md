---
id: proc-lint-pass
type: procedural
title: Lint Pass Workflow — V2
created: 2026-10-08
updated: 2026-10-08
author: system
confidence: high
ttl: permanent
status: active
tags: [lint, maintenance, lifecycle, v2]
related: [proc-inbox-promotion, proc-memory-assembly, proc-session-loop]
source: system
---

# Workflow: Lint Pass — V2

## Purpose

The lint pass is the vault's health and lifecycle management routine. V2 introduces checkpointed execution, tiered inbox promotion, episodic compression quality gate, stale file renewal enforcement, and log pruning. Each step logs a checkpoint before proceeding — if any step fails the lint pass stops and reports rather than continuing silently.

## Trigger Condition

- Scheduled: daily for active systems, weekly for low-activity vaults
- Manual: triggered by Prompt 5 of the session loop or on demand
- Never runs automatically without being triggered

## Prerequisites

- Access to all vault folders
- Current date for TTL calculations
- Write access to `_meta/log.md` and `_meta/index.md`

---

## Steps

### STEP 1 — Load Schema [CHECKPOINT 1]
Read `_meta/CLAUDE.md` in full. Confirm current version and rules.
Log: `[DATE] | LINT-CP1 | schema loaded | version: X.X.X`
If CLAUDE.md cannot be read → STOP and report.

### STEP 2 — TTL Scan [CHECKPOINT 2]
For every file in `semantic/`, `episodic/`, `retrieval/`, `prospective/`:
- Calculate expiry: `created` date + `ttl` value
- If expired AND `status: active` → set `status: stale`
- If `status: stale` for more than 14 days with no renewal → move to `archive/`, log `AUTO-ARCHIVE`
- Do NOT archive files stale for less than 14 days — flag only
Log: `[DATE] | LINT-CP2 | stale-flagged:N | auto-archived:N`

### STEP 3 — Tiered Inbox Processing [CHECKPOINT 3]
For each file in `_inbox/` with `status: pending`:

**Tier 1 check — auto-promote if ALL true:**
- `confidence: high`
- `source_type: USER` or `source_type: EXTERNAL_SOURCE`
- No duplicate detected in target folder
→ Promote to correct memory folder, set `status: active`, log `AUTO-PROMOTE`

**Tier 2 check — add to approval summary if:**
- `confidence: medium` with `source_type: USER`
- Any `source_type: AI` item
- Any DUPLICATE_CANDIDATE
- Any CONTRADICTION flagged
→ Keep in `_inbox/`, add to Tier 2 approval summary for human

**Tier 3 check — move to batch review if:**
- `confidence: low`
- `source_type: INFERENCE`
- UNVERIFIED items
→ Move to `_inbox/review/`, set `status: review`, log `BATCH-REVIEW`

Log: `[DATE] | LINT-CP3 | auto-promoted:N | tier2-queued:N | tier3-review:N`

### STEP 4 — Conflict Detection [CHECKPOINT 4]
Scan `semantic/` for contradictory facts on the same topic:
- If conflict found: newer `updated:` date wins → older file set `status: archived`, moved to `archive/`
- If same date: flag both for human review, do not auto-resolve
Log: `[DATE] | LINT-CP4 | conflicts-resolved:N | conflicts-flagged:N`

### STEP 5 — Episodic Compression Candidates [CHECKPOINT 5]
For episodic files where `created` date is more than 90 days ago:
- Generate a compression summary: key facts, decisions, outcomes in 5-10 bullets
- Present summary to human with prompt:
  > "This episodic log is ready for compression. Approve to extract these facts into semantic memory, or reject to keep the full log active."
- If approved: create new semantic file with extracted facts, move original to `archive/`
- If rejected: update `created:` date to reset the 90-day clock
- Never compress without explicit human approval
Log: `[DATE] | LINT-CP5 | compression-approved:N | compression-rejected:N`

### STEP 6 — Orphan Detection [CHECKPOINT 6]
Files with no `related:` links AND not listed in `_meta/index.md`:
- Flag for human review — do not auto-archive
- Report in lint summary with filename and creation date
Log: `[DATE] | LINT-CP6 | orphans-found:N`

### STEP 7 — Prospective Tasks [CHECKPOINT 7]
For files in `prospective/`:
- Tasks with `status: complete` → move to `archive/`, log `ARCHIVE`
- Tasks with past deadline AND `status: active` → set `status: stale`, flag for review
- Tasks with deadlines within 7 days → highlight in lint summary
Log: `[DATE] | LINT-CP7 | tasks-archived:N | tasks-overdue:N | tasks-due-soon:N`

### STEP 8 — Stale Renewal Check [CHECKPOINT 8]
For all files with `status: stale`:
- If stale for less than 14 days → include in human review summary with renewal prompt
- If stale for more than 14 days → auto-archive, log `AUTO-ARCHIVE`
Renewal prompt to human:
> "These files are stale but within the 14-day renewal window. Renew (reset TTL) or archive each one."
Log: `[DATE] | LINT-CP8 | renewed:N | auto-archived-stale:N`

### STEP 9 — Log Pruning [CHECKPOINT 9]
For log entries in `_meta/log.md` older than 90 days:
- Summarise into a monthly summary entry: "Month YYYY-MM: N actions — promoted:N archived:N linted:N"
- Replace individual old entries with the monthly summary
- This keeps log.md readable and context-window-efficient
Log: `[DATE] | LINT-CP9 | entries-pruned:N | summaries-created:N`

### STEP 10 — Rebuild Index [CHECKPOINT 10]
Update `_meta/index.md`:
- List all active files by memory type with one-line descriptions
- Update vault stats: total files, by type, by status
- Add vault health rating: Green / Amber / Red based on inbox count, stale count, last lint date
Log: `[DATE] | LINT-CP10 | index-rebuilt | health: GREEN/AMBER/RED`

### STEP 11 — Write Lint Summary [CHECKPOINT 11]
Append full summary to `_meta/log.md`:
```
[DATE HH:MM] | LINT-COMPLETE | author | vault |
  auto-promoted:N | tier2-queued:N | tier3-review:N |
  conflicts-resolved:N | compression-candidates:N |
  orphans:N | tasks-archived:N | stale-archived:N |
  log-pruned:N | health:GREEN/AMBER/RED
```
Present Tier 2 approval queue to human if any items exist.

---

## Expected Output

- `_inbox/` contains only Tier 2 items awaiting human approval
- `_inbox/review/` contains Tier 3 batch review items
- No auto-archivable stale files remain active
- `_meta/index.md` reflects current vault state with health rating
- `_meta/log.md` has lint summary entry and is pruned to 90-day window
- Human has received Tier 2 approval queue if applicable
- Human has received compression summaries if applicable

## Edge Cases

- **Conflict between two HIGH confidence USER files of same date** → flag for human, do not auto-resolve
- **_inbox/ item matches multiple existing files** → flag for human review
- **Episodic file with open prospective tasks** → do not compress until tasks are moved to prospective/
- **Step fails mid-lint** → stop at that checkpoint, report failure, do not proceed

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-10-08 | system | Initial version |
| 2.0 | 2026-10-08 | system | V2 — tiered promotion, 11 checkpoints, episodic compression quality gate, stale renewal enforcement, log pruning, vault health rating |
