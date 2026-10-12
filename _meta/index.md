# Vault Index
> Auto-maintained by lint pass. Last updated: 2026-10-08 | Schema version: 2.0.0

This file is the content catalog of the entire vault. Every active memory file is listed here. Use this file to navigate the vault and decide what to load into working memory.

---

## Root Files

| File | Description |
|---|---|
| `PROMPTS.md` | Step-by-step prompts guide v1.2.0 — includes Part 0 extraction workflow |
| `SYSTEM-EXPLAINER.md` | Full system documentation v2.2.0 |
| `CHAT-EXTRACTION-PROMPT.md` | Paste into any external AI chat to extract and import knowledge |
| `README.md` | GitHub repo README |

---

## _meta (System Files)

| File | Description |
|---|---|
| `_meta/CLAUDE.md` | Schema v2.2.0 — rules engine, prompting loops, agent autonomy levels, extraction Rule 12 |
| `_meta/index.md` | This file — vault content catalog and project topic map |
| `_meta/log.md` | Append-only action log |
| `_meta/models.md` | Registry of AI models that have accessed this vault |

---

## Project Topic Map

> Populated during new project initialisation. Records the approved topic structure for this vault.

*Not yet initialised. Run `procedural/new-project-init.md` to seed this vault for a specific project.*

---


---

## Vault Health Status

| Metric | Value |
|---|---|
| Health Rating | 🟢 Green |
| Last Lint Pass | Not yet run |
| Inbox Pending | 0 |
| Review Pile | 0 |
| Stale Files | 0 |
| Overdue Tasks | 0 |

> Updated automatically by lint pass. Run "vault health check" prompt for current status.

---

## Agent Autonomy Level

**Current level:** 1 — Assisted
*Tier 1 items auto-promote. Tier 2 items require human approval. Tier 3 goes to batch review.*

## _inbox (Pending Promotion)

*Empty — no items awaiting lint review.*

### _inbox/review/ (Batch Review Pile)

*Empty — no Tier 3 items pending weekly review.*

---

## semantic/ (Durable Facts)

*No entries yet. Topic map pending project initialisation.*

---

## episodic/ (Event Logs)

*No entries yet.*

---

## procedural/ (Workflows)

| File | Description |
|---|---|
| `procedural/session-loop.md` | V2 — 5-prompt session loop with tiered promotion, optional maintenance prompts |
| `procedural/new-project-init.md` | The 5-prompt new project initialisation loop — run once per new vault |
| `procedural/lint-pass.md` | V2 — 11-checkpoint lint pass with tiered promotion and compression quality gate |
| `procedural/inbox-promotion.md` | V2 — tiered promotion: Tier 1 auto-promote, Tier 2 human queue, Tier 3 batch review |
| `procedural/memory-assembly.md` | V2 — relevance scoring, file limits, continuity snapshot protocol |

---

## retrieval/ (Reference Docs)

*No entries yet.*

---

## prospective/ (Task Queue)

*No active tasks.*

---

## archive/ (Expired/Superseded)

*Empty.*

---

## Vault Stats

| Metric | Count |
|---|---|
| Total active files | 10 |
| Semantic memories | 0 |
| Episodic logs | 0 |
| Procedural workflows | 5 |
| Retrieval docs | 0 |
| Prospective tasks | 0 |
| Archived files | 0 |
| Inbox pending | 0 |

---

## Agent Autonomy Level

**Current level:** 1 — Assisted (human approves every write)
*Change this setting deliberately after trust is established. See CLAUDE.md → Agent Autonomy Levels.*
