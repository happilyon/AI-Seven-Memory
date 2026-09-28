# Vault Index
> Auto-maintained by lint pass. Last updated: 2026-09-15 | Schema version: 1.0.0

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
| `_meta/CLAUDE.md` | Schema v1.0.0 — rules engine, prompting loops, agent autonomy levels, extraction Rule 12 |
| `_meta/index.md` | This file — vault content catalog and project topic map |
| `_meta/log.md` | Append-only action log |
| `_meta/models.md` | Registry of AI models that have accessed this vault |

---

## Project Topic Map

> Populated during new project initialisation. Records the approved topic structure for this vault.

*Not yet initialised. Run `procedural/new-project-init.md` to seed this vault for a specific project.*

---

## _inbox (Pending Promotion)

*Empty — no items awaiting lint review.*

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
| `procedural/session-loop.md` | The 5-prompt standard session loop — core UX for every session |
| `procedural/new-project-init.md` | The 5-prompt new project initialisation loop — run once per new vault |
| `procedural/lint-pass.md` | Step-by-step lint pass workflow |
| `procedural/inbox-promotion.md` | How to promote _inbox items to memory folders |
| `procedural/memory-assembly.md` | How to assemble working memory per session |

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
