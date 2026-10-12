---
id: proc-inbox-promotion
type: procedural
title: Inbox Promotion Workflow — V2
created: 2026-10-08
updated: 2026-10-08
author: system
confidence: high
ttl: permanent
status: active
tags: [inbox, promotion, lifecycle, tiered, v2]
related: [proc-lint-pass, proc-memory-assembly, proc-session-loop]
source: system
---

# Workflow: Inbox Promotion — V2

## Purpose

Controls how new memories move from `_inbox/` into active memory folders using the V2 tiered promotion system. Prevents approval fatigue by ensuring only genuinely ambiguous items reach the human review queue.

## Trigger Condition

- During lint pass Step 3
- Or manually when fast-tracking a specific inbox item

---

## The Three Tiers

### Tier 1 — Auto-Promote

**Criteria — ALL must be true:**
1. `confidence: high`
2. `source_type: USER` or `source_type: EXTERNAL_SOURCE`
3. No duplicate detected in target folder
4. No contradiction with existing semantic memory

**Action:**
- Move file to correct memory folder based on `type:` field
- Set `status: active`
- Update `updated:` to today
- Add to `_meta/index.md`
- Update `related:` fields on connected files
- Log `AUTO-PROMOTE` in `_meta/log.md`

No human involvement needed.

---

### Tier 2 — Human Approval Queue

**Criteria — any ONE triggers Tier 2:**
- `confidence: medium` with `source_type: USER`
- Any `source_type: AI` regardless of confidence
- Flagged as DUPLICATE_CANDIDATE
- Contains a CONTRADICTION with existing memory
- Extraction file section where AI recommendation conflicts with user decision

**Action:**
- Keep in `_inbox/` with `status: pending`
- Add to Tier 2 approval summary presented at session close
- Present to human with: filename, confidence, source_type, one-line summary, recommended action

**Human responses:**
- APPROVE → promote as Tier 1
- EDIT → human edits content, then promote
- REJECT → delete from inbox, log `REJECT`
- DEFER → keep in inbox for next session

---

### Tier 3 — Batch Review Pile

**Criteria — any ONE triggers Tier 3:**
- `confidence: low`
- `source_type: INFERENCE`
- Flagged UNVERIFIED
- Episodic compression candidate

**Action:**
- Move to `_inbox/review/` subfolder
- Set `status: review`
- Log `BATCH-REVIEW`
- Human reviews weekly, not per session

**Weekly batch review prompt:**
> "Show me all items in _inbox/review/. For each one: filename, one-line summary, confidence, source type, recommended action. I will approve, edit, or reject each one."

---

## Duplicate Check

Before promoting any item, check target folder for duplicates:
- **Identical content** → reject inbox item, log `REJECT`
- **Complementary content** → merge into existing file, update `updated:` date, log `MERGE`
- **Near-duplicate with lower confidence** → flag as DUPLICATE_CANDIDATE → Tier 2
- **Genuinely new** → proceed with tier assessment

---

## Target Folder Routing

| `type:` field | Target folder |
|---|---|
| semantic | `semantic/` |
| episodic | `episodic/` |
| procedural | `procedural/` |
| retrieval | `retrieval/` |
| prospective | `prospective/` |
| extraction | Process section by section — each section routed individually |

---

## Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-10-08 | system | Initial version |
| 2.0 | 2026-10-08 | system | V2 — full tiered promotion system replacing flat approval queue |
