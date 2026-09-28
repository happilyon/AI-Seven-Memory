---
id: proc-inbox-promotion
type: procedural
title: Inbox Promotion Workflow
created: 2026-09-15
updated: 2026-09-15
author: system
confidence: high
ttl: permanent
status: active
tags: [inbox, promotion, lifecycle]
related: [proc-lint-pass, proc-memory-assembly]
source: system
---

# Workflow: Inbox Promotion

## Purpose

Controls how new memories move from `_inbox/` staging into the active memory folders. Ensures no unvalidated content enters the vault directly.

## Trigger Condition

- During lint pass (Step 3)
- Or manually when a human or agent wants to fast-track a specific inbox item

## Steps

1. **Open the inbox item**
   Read the file. Confirm frontmatter is complete and valid.
   Missing required fields → reject with reason logged.

2. **Determine target folder**
   Based on the `type:` field:
   - `semantic` → `semantic/`
   - `episodic` → `episodic/`
   - `procedural` → `procedural/`
   - `retrieval` → `retrieval/`
   - `prospective` → `prospective/`

3. **Run duplicate check**
   Search target folder for files with similar `title:` or overlapping `tags:`.
   If near-duplicate found: compare content.
   - Identical content → reject inbox item, log `REJECT`
   - Complementary content → merge into existing file, update `updated:` date, log `MERGE`
   - Genuinely new → proceed to promotion

4. **Run conflict check (semantic only)**
   If type is `semantic`: search for files asserting contradictory facts.
   If conflict found → flag both files, do not auto-promote, log conflict for review.

5. **Promote the file**
   - Move file from `_inbox/` to target folder
   - Set `status: active`
   - Update `updated:` to today's date
   - Add to `_meta/index.md`
   - Update `related:` fields on connected files
   - Log `PROMOTE` action in `_meta/log.md`

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-09-15 | system | Initial release |
