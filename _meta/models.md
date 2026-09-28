---
id: models-registry
type: procedural
title: AI Model Registry
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: system
confidence: high
ttl: permanent
status: active
tags: [meta, registry, models]
related: []
source: system
---

# AI Model Registry

Every AI model that reads from or writes to this vault must register a session
entry here. This file ensures provenance tracking in multi-model environments.

---

## Registration Format

When starting a session, append an entry to the Active Sessions table below.
When ending a session, move the entry to Session History and add an end timestamp.

```
| model-name | version | session-id | start | end | files-read | files-written | notes |
```

---

## Active Sessions

| Model | Version | Session ID | Start | End | Files Read | Files Written | Notes |
|---|---|---|---|---|---|---|---|
| *none* | — | — | — | — | — | — | — |

---

## Session History

| Model | Version | Session ID | Start | End | Files Read | Files Written | Notes |
|---|---|---|---|---|---|---|---|
| *none yet* | — | — | — | — | — | — | — |

---

## Registered Models

Models that have been granted access to this vault:

| Model | Provider | IDE / Interface | Access Level | First Registered |
|---|---|---|---|---|
| *Register your first model by running Step 1 of PROMPTS.md* | — | — | — | — |

> To register a new model, follow the prompt in PROMPTS.md Part 1 Step 1.
> The AI will register itself automatically on first session start.
