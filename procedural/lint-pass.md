---
id: proc-lint-pass
type: procedural
title: Lint Pass Workflow
created: 2026-09-15
updated: 2026-09-15
author: system
confidence: high
ttl: permanent
status: active
tags: [lint, maintenance, lifecycle]
related: [proc-inbox-promotion, proc-memory-assembly]
source: system
---

# Workflow: Lint Pass

## Purpose

The lint pass is the vault's health and lifecycle management routine. It keeps memory accurate, current, and non-redundant. It is the primary mechanism for TTL enforcement, conflict resolution, and episodic compression.

## Trigger Condition

- Scheduled: daily for active systems, weekly for low-activity vaults
- Manual: any time a human or agent suspects stale or conflicting memory

## Prerequisites

- Access to all vault folders
- Current date for TTL calculations
- Write access to `_meta/log.md` and `_meta/index.md`

## Steps

1. **Load the schema**
   Read `_meta/CLAUDE.md` to confirm current rules before proceeding.

2. **Scan for TTL expiry**
   For every file in `semantic/`, `episodic/`, `retrieval/`, `prospective/`:
   - Calculate: `created` date + `ttl` value
   - If expiry date < today AND status is `active`: set `status: stale`
   - Flag stale files for human or agent review
   - Do not archive automatically — require confirmation

3. **Process _inbox/ items**
   For each file in `_inbox/` with `status: pending`:
   - Check for duplicates: does a semantically identical file exist in the target folder?
     - If duplicate: merge or reject, log `MERGE` or `REJECT` action
     - If unique: promote to correct memory folder, set `status: active`, log `PROMOTE`
   - Update `_meta/index.md` to include promoted file
   - Update `related:` fields on any connected files

4. **Detect conflicts**
   Scan `semantic/` for files where:
   - Two files assert contradictory facts about the same entity or topic
   - Resolution: newer `updated:` date wins; older file gets `status: archived` and moves to `archive/`
   - Log `ARCHIVE` action with conflict reason in notes

5. **Compress old episodic files**
   For episodic files where `created` date > 90 days ago:
   - Summarize key facts, decisions, and outcomes into a new `semantic/` file
   - New semantic file's `source:` field references original episodic `id`
   - Move original episodic file to `archive/`, set `status: archived`
   - Log `ARCHIVE` and `CREATE` actions

6. **Identify orphan files**
   Files with no `related:` links and not listed in `_meta/index.md`:
   - Flag for review — they may be valid but unconnected, or may be duplicates
   - Do not auto-archive orphans

7. **Check prospective tasks**
   For files in `prospective/`:
   - Tasks marked complete: move to `archive/`
   - Tasks with past deadline dates: flag as stale
   - Active tasks with upcoming deadlines: ensure they appear in index

8. **Rebuild index**
   Update `_meta/index.md` to reflect all current active files.
   Update vault stats table.

9. **Write lint log entry**
   Append summary to `_meta/log.md`:
   ```
   [YYYY-MM-DD HH:MM] | LINT | author | vault | promoted:N archived:N conflicts:N compressed:N orphans:N
   ```

## Expected Output

- `_inbox/` is empty (all items promoted, merged, or rejected)
- No active files with expired TTL (all flagged or archived)
- No unresolved conflicts in `semantic/`
- `_meta/index.md` is current
- `_meta/log.md` has lint summary entry

## Edge Cases & Failure Modes

- **Conflict between two high-confidence files of same date:** flag for human review; do not auto-resolve
- **_inbox/ item matches multiple existing files:** flag for human review
- **Episodic file with important open tasks:** do not compress until tasks are moved to `prospective/`

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-09-15 | system | Initial release |
