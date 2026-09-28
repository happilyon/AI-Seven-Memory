# 🧠 Universal AI Memory Vault

> A model-agnostic, persistent memory system for AI agents and humans — built entirely on plain markdown files.

---

## What Is This?

The Universal AI Memory Vault solves the biggest problem with AI models today — **they forget everything between sessions**.

This system gives any AI model (Claude, GPT, Gemini, or any local model) a structured, persistent memory that survives across sessions, model changes, and system restarts. It implements all **7 cognitive memory types** as a plain markdown folder system that lives in Obsidian and is orchestrated by Claude Code or any AI agent.

Every memory is a file. The files are the system. No database, no cloud service, no vendor lock-in.

---

## The Problem It Solves

Every time you start a new AI chat session you re-explain yourself from scratch. Your preferences, past decisions, ongoing projects, and accumulated knowledge are gone. This compounds into wasted time, repeated mistakes, and zero learning across sessions.

This vault fixes that permanently.

---

## The 7 Memory Types

| Folder | Memory Type | What Gets Stored |
|---|---|---|
| `semantic/` | Semantic | Durable facts, preferences, project knowledge |
| `episodic/` | Episodic | Dated session logs, decisions, outcomes |
| `procedural/` | Procedural | Workflows, rules, repeatable processes |
| `retrieval/` | Retrieval | External reference docs and sources |
| `prospective/` | Prospective | Tasks, intentions, scheduled actions |
| *(context window)* | Working | Assembled per session — never stored |
| *(model weights)* | Parametric | Frozen in the model — never stored |

---

## The 5 Core Use Cases

| Use Case | What It Remembers |
|---|---|
| **Personal AI Assistant** | User profile, preferences, life context, ongoing projects |
| **Software Development** | Tech stack, architecture decisions, team, conventions |
| **Research & Knowledge** | Hypotheses, sources, findings, open questions |
| **Client & Business Management** | Client profile, history, engagements, commercial terms |
| **Content Creation** | Voice, style, audience, topics covered, content calendar |

Use cases are extensible — add your own by following the prompt in `PROMPTS.md`.

---

## Key Features

- **Model-agnostic** — works with Claude, GPT, Gemini, or any local model
- **Plain markdown** — every memory is a human-readable file, no proprietary format
- **Lifecycle management** — TTL fields and a lint pass keep memory accurate over time
- **Topic-chunked files** — token-efficient selective loading, not bulk context dumps
- **Inbox staging** — nothing enters memory without validation and human approval
- **Multi-model support** — multiple AI models share one vault with full provenance tracking
- **5-prompt session loop** — simple enough for any non-technical user to operate
- **Obsidian native** — full graph view, backlinks, and tag search out of the box

---

## How To Get Started

### 1. Clone The Repo
```bash
git clone https://github.com/yourusername/ai-memory-vault.git
```

### 2. Open In Obsidian
- Open Obsidian
- Click **Open folder as vault**
- Select the cloned folder
- Go to Settings → Files & Links → enable **Use [[Wikilinks]]**

### 3. Read PROMPTS.md
Everything you need is in `PROMPTS.md` — copy-and-paste prompts for setup, every session, and maintenance. No technical knowledge required.

### 4. Run The New Project Init
Follow **Part 1** of `PROMPTS.md` to seed your vault for your first project. Takes about 10 minutes.

### 5. Use The Session Loop
From your second session onwards, use the **5-prompt session loop** in Part 2 of `PROMPTS.md`. Same 5 prompts every time.

---

## Vault Structure

```
vault/
├── PROMPTS.md                ← Start here — all copy-paste prompts
├── SYSTEM-EXPLAINER.md       ← Full system documentation
├── _meta/                    ← System control files
│   ├── CLAUDE.md             ← Rules engine — AI reads this first
│   ├── index.md              ← Vault content catalog
│   ├── log.md                ← Append-only action log
│   └── models.md             ← AI model registry
├── _inbox/                   ← Staging — all new memory lands here first
├── semantic/                 ← Durable facts (topic-chunked)
├── episodic/                 ← Session and event logs
├── procedural/               ← Workflows and rules
├── retrieval/                ← External reference documents
├── prospective/              ← Tasks and future intentions
└── archive/                  ← Expired or superseded memories
```

---

## The 5-Prompt Session Loop

Every session runs the same 5 prompts:

| # | Prompt | Purpose |
|---|---|---|
| 1 | `"Start a new session. Load memory and summarise what you know."` | Load context |
| 2 | *(normal conversation)* | Do the work |
| 3 | `"What should we save from this session?"` | Capture new memories |
| 4 | `"Show me exactly what you'll write before saving."` | Review before commit |
| 5 | `"Approved — write to _inbox/, run lint pass, close session."` | Commit and close |

Full prompts with exact wording in `PROMPTS.md`.

---

## How Memory Stays Accurate Over Time

Every memory file has a `ttl` (time-to-live) field. The **lint pass** runs on a schedule and:

- Flags expired memories as stale for human review
- Promotes validated inbox items to active memory folders
- Detects and resolves conflicts between contradictory facts
- Compresses old episodic logs into permanent semantic memories
- Keeps the vault index current

Nothing is ever permanently deleted — superseded memories move to `archive/`.

---

## Multi-Model Support

Multiple AI models can share the same vault simultaneously. Each model:
- Registers its session in `_meta/models.md`
- Writes only to `_inbox/` — never directly to memory folders
- Tags every file it creates with its model name in the `author:` field
- Follows the rules in `_meta/CLAUDE.md`

The vault is the shared brain. Any model that reads CLAUDE.md can operate in it correctly.

---

## Roadmap

| Version | Focus | Status |
|---|---|---|
| **V1 — Current** | Core vault template, 7 memory types, session loop, 5 use cases | ✅ Complete |
| **V2 — Near term** | Claude Code automation scripts, vector search layer, .gitignore template | 🔜 Planned |
| **V3 — Mid term** | Background lint agent, multi-agent orchestration, additional use case templates | 🔜 Planned |
| **V4 — Long term** | Web/mobile interface, hosted SaaS, model-agnostic API, fine-tuning pipeline | 🔜 Planned |

---

## Documentation

| File | What It Contains |
|---|---|
| `PROMPTS.md` | All copy-paste prompts — start here |
| `SYSTEM-EXPLAINER.md` | Complete system documentation — architecture, design decisions, glossary |
| `_meta/CLAUDE.md` | Rules engine — the AI reads this to understand how to operate the vault |
| `procedural/session-loop.md` | Full session loop workflow |
| `procedural/new-project-init.md` | New project setup with topic map examples for all 5 use cases |
| `procedural/lint-pass.md` | Lint pass workflow |

---

## Inspiration & Credits

This system was designed drawing on:
- The **7 memory types framework** for agentic AI systems
- **Andrej Karpathy's llm-wiki** pattern — compounding knowledge via structured markdown
- The principle that memory should be staged, lifecycle-managed, and model-agnostic

---

## Contributing

Contributions welcome — especially:
- New use case templates
- Claude Code automation scripts for the session loop and lint pass
- Vector search integration guides
- Translations of PROMPTS.md for non-English users

Please read `SYSTEM-EXPLAINER.md` before contributing so your additions follow the schema and design principles.

---

## License

MIT — use freely, build on it, make it yours.

---

*Current schema version: 1.0.0 — see `_meta/CLAUDE.md` for changelog*

---

## Chat Knowledge Extraction

Before using the vault for the first time — or any time you finish a valuable chat on an external AI platform — use the built-in extraction prompt to capture everything worth keeping.

### How It Works

1. Finish a chat on any platform (ChatGPT, Perplexity, Gemini, Claude, etc.)
2. Open `CHAT-EXTRACTION-PROMPT.md` from this repo
3. Copy the extraction prompt and paste it at the **end of that same chat**
4. The AI reads its own conversation and outputs a structured markdown extraction file
5. Save the output as `extraction-YYYY-MM-DD-topic.md` into your vault `_inbox/` folder
6. Run the lint pass — knowledge is automatically promoted to the correct memory folders

### What Gets Extracted

| Section | What It Contains |
|---|---|
| Summary | 3-sentence overview of the entire chat |
| Key Knowledge | Facts sorted by all 7 memory types |
| Decisions Made | Confirmed conclusions with confidence ratings |
| Recommendations | Actionable guidance with adoption status |
| Open Questions | Unresolved threads needing follow-up |
| Contradictions | Where the chat reversed or was uncertain |
| Next Steps | Prioritised action list |
| Next Session Primer | Ready-to-paste context block for your next chat |

### Why This Matters

This turns every AI conversation you have ever had into potential vault memory. Whether you are seeding a brand new vault or capturing insights from an ongoing project — the extraction prompt works on any platform, with any AI model, at any time.

See `CHAT-EXTRACTION-PROMPT.md` for the full prompt and instructions.
