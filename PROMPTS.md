# Universal AI Memory Vault — Step By Step Prompts Guide

> **Version:** 1.0.0 | **Created:** 2026-09-15 | **Updated:** 2026-09-15
>
> This is your complete copy-and-paste prompt guide. Every prompt you need to run this system is here in order. You do not need to read any other file to get started — just follow the steps below.
>
> **How to use this file:** Copy each prompt exactly as written, fill in anything inside [square brackets], and paste it into your AI chat. Do not skip steps.
>
> **IDE & Model Support:** This system works in any IDE or chat interface including VS Code, Antigravity IDE, Cursor, or any web-based AI chat. Compatible with all AI models including Claude (Opus, Sonnet, Haiku), Gemini, GPT, and local models like Qwen3-coder. Every file created by an AI model is tagged with the model name in the `author:` frontmatter field for full provenance tracking across models.

---

## Before You Begin — One Time Setup Checklist

Complete these steps once before running any prompts:

- [ ] Download the vault template files
- [ ] Create a folder on your computer named `ai-memory-vault` (or your project name)
- [ ] Place all vault files inside it, keeping the exact folder structure intact
- [ ] Open Obsidian → click **Open folder as vault** → select your vault folder
- [ ] In Obsidian Settings → Files & Links → enable **Use [[Wikilinks]]**
- [ ] Confirm you can see all folders: `_meta`, `_inbox`, `semantic`, `episodic`, `procedural`, `retrieval`, `prospective`, `archive`
- [ ] Open your AI chat or IDE (Antigravity, VS Code, Claude.ai, ChatGPT, or any interface)
- [ ] Upload or paste the contents of `_meta/CLAUDE.md` into the chat so the AI can read the rules
- [ ] Note which AI model you are using — you will need this for Step 1

You are now ready. Follow the steps below in order.

---

---

## PART 0 — Chat Knowledge Extraction
### Run this on any external AI chat BEFORE importing to the vault

This part is optional but highly recommended — especially when seeding a new vault
or after any valuable chat on an external platform.

---

### How To Use The Extraction Prompt

**Step 1 — Find a valuable chat to extract**
Go to any AI platform where you have had a useful conversation —
ChatGPT, Perplexity, Gemini, Claude, or any other.

**Step 2 — Open CHAT-EXTRACTION-PROMPT.md**
Find it in your vault root folder.

**Step 3 — Fill in the three fields at the top of the prompt**
- Your AI model name and version
- Today's date
- A short topic or project name for this chat

**Step 4 — Copy and paste the prompt at the END of that chat**
The AI will read its own conversation and generate a structured extraction.

**Step 5 — Review the output**
Check the extracted knowledge for accuracy. Edit anything wrong or missing.

**Step 6 — Save as a markdown file**
Name it: `extraction-YYYY-MM-DD-topic.md`
Save it directly into your vault `_inbox/` folder in Obsidian.

**Step 7 — Check the Extraction Quality Check table**
Before saving, scroll to the bottom of the extraction output and review
the 12-item quality check table. Any FAIL item needs attention before
you import the file into the vault.

**Step 8 — Check Sensitive Data Detected section**
Confirm the secrets section says "None detected" or that all redactions
are in place. Never import a file with unredacted secrets or PII.

**Step 9 — Run the lint pass**
The lint pass will split the extraction by memory type and promote each
piece to the correct memory folder. It will flag MEDIUM/LOW confidence
items, INFERENCE items, and DUPLICATE_CANDIDATEs for your review.

**Step 10 — Use the Next Session Primer or AI Handoff**
For a new human chat: copy the Next Session Primer (under 200 words)
and paste it at the start of your next session on any platform.
For an AI agent: use the AI HANDOFF yaml block as the context input.

---

### Quick Extraction Prompt Reference

Open `CHAT-EXTRACTION-PROMPT.md` and copy the full prompt from there.
Fill in these three fields before pasting into your external chat:

```
- Your name and version: [e.g. ChatGPT-4o / Gemini 2.5 Pro / Perplexity]
- Today's date: [YYYY-MM-DD]
- Chat topic or project name: [brief description]
```

---

## PART 1 — New Project Setup
### Run these prompts once when starting any new project

---

> **Single project rule:** This vault is for one project only. If you are starting a new unrelated project, create a fresh copy of the vault template rather than adding it here.

### Step 1 — Introduce The System To The AI And Register The Model

Copy and paste this prompt first in every new chat session with a new AI model.
**Fill in your IDE name and exact model name before sending:**

```
You are operating inside a Universal AI Memory Vault — a structured markdown-based 
memory system. The rules for this system are defined in CLAUDE.md which I have 
provided. Please read and confirm you understand the following before we proceed:

1. All new memory goes to _inbox/ first — never directly to memory folders
2. TEMPLATE.md files are read-only masters — always duplicate and rename
3. Every file needs the correct frontmatter schema
4. You must propose before you write — nothing gets saved without my approval
5. Both new-project-init.md and SYSTEM-EXPLAINER.md must stay in sync for use cases
6. You must record your exact model name and version in the author: field of every 
   file you create or modify — this is mandatory for provenance tracking

I am using the following setup:
- IDE / Interface: [e.g. Antigravity IDE / VS Code / Claude.ai / ChatGPT]
- AI Model: [e.g. claude-sonnet-4-6 / gemini-2.5-pro / qwen3-coder:480b-cloud]

Please:
1. Confirm you have read and understood CLAUDE.md
2. Confirm your exact model name as you will record it in the author: field
3. Confirm you are ready to begin the new project setup
```

**Wait for:** AI confirmation of rules understood, model name confirmed, and ready to proceed.

**Note:** If you switch to a different model mid-project, run this Step 1 prompt again with the new model name before continuing. This ensures every file is correctly attributed.

---

### Step 2 — Provide Project Context & Request Topic Map

Fill in the brackets and send:

```
This is a new [choose one: personal assistant / software project / research project / 
client management system / content creation system / other — describe it].

Here is everything relevant about this project:

[Paste all context here — be as detailed as possible. Include:
- What this project is about
- Goals and objectives
- Key people involved
- Important facts, constraints, or preferences
- Any existing knowledge you want the system to remember
- Tools, platforms, or systems being used
- Any decisions already made]

Analyse this context and propose a topic map for the semantic memory files.
For each proposed file, give me the filename and a one-line description of 
what it will contain. Do not create any files yet. Wait for my approval.
```

**Wait for:** A proposed topic map list from the AI.

---

### Step 3 — Review And Approve The Topic Map

Read the proposed topic map carefully. Then send one of these:

**If you approve as-is:**
```
Topic map approved as proposed. Proceed to generate the files.
```

**If you want changes:**
```
Here is my revised topic map — please use this instead:

[Paste your edited version of the topic map]

Confirmed. Proceed to generate the files based on this revised map.
```

**Wait for:** AI confirmation of the approved topic map.

---

### Step 4 — Generate Initial Memory Files

Send:

```
Generate the initial semantic memory files based on the approved topic map. 
Write them all to _inbox/ with:
- status: pending
- author: [the exact model name you confirmed in Step 1]

Use the topic-chunking rules — group related facts together in each file.
Fill in as much as you can from the context I provided.
Flag any fields where you need more information from me.
Do not promote anything yet — everything stays in _inbox/.
```

**Wait for:** Confirmation that files have been written to `_inbox/`.

---

### Step 5 — Review Each Generated File

Send:

```
Show me each generated _inbox/ file one at a time, starting with the first one. 
Present the full content including frontmatter. Wait for my approval, edit 
instruction, or rejection before showing the next file.
```

**For each file the AI shows you, respond with one of:**

```
Approved. Show me the next file.
```
```
Edit this file: [describe your changes]. Then show me the next file.
```
```
Reject this file — do not include it. Show me the next file.
```

**Wait for:** All files reviewed and resolved.

---

### Step 6 — Commit And Confirm Vault Ready

Send:

```
All files reviewed. Now:
1. Run the lint pass to promote all approved _inbox/ files to their correct folders
2. Update _meta/index.md with the project topic map and current vault stats
3. Set the agent autonomy level to Level 1 in _meta/index.md
4. Register this session in _meta/models.md including:
   - Model name and version: [your model name]
   - IDE / Interface: [your IDE name]
   - Session start time
5. Write a SESSION_END entry to _meta/log.md including the model name used
6. Confirm the vault is ready for regular sessions with a summary of what was created
```

**Wait for:** Vault ready confirmation and summary.

---

### ✅ Setup Complete

Your vault is now live. Save all files back to your Obsidian vault folder.
From this point forward, use **Part 2** for every session.

---

## PART 2 — Standard Session Loop
### Use these 5 prompts at the start of every session

> **Before every session:** Note which IDE and AI model you are using. If it is different from the last session, run Part 1 Step 1 first to register the new model before continuing with the session loop below.

---

### Prompt 1 — Start Session

Copy and send at the beginning of every session.
**Fill in your current IDE and model name:**

```
Start a new session.

My current setup:
- IDE / Interface: [e.g. Antigravity IDE / VS Code / Claude.ai]
- AI Model: [e.g. claude-sonnet-4-6 / gemini-2.5-pro / qwen3-coder:480b-cloud]

Please:
1. Read _meta/CLAUDE.md and confirm you are following the current schema version
2. Read _meta/index.md to understand the vault structure and topic map
3. Read _meta/models.md and confirm this model is registered — if not, register it now
4. Load the relevant semantic memory files for this session
5. Load the 3 most recent episodic log entries
6. Load any active prospective tasks
7. Log SESSION_START in _meta/log.md with your model name and IDE
8. Give me a summary of what you currently know about this project

Record your model name as [model name] in the author: field of any files 
you create or modify this session. Wait for my confirmation before we begin work.
```

**Wait for:** A memory summary from the AI. Read it and confirm it looks correct.

**If something is wrong or outdated in the summary:**
```
The following information is outdated or incorrect: [describe what's wrong].
Please note this correction before we proceed. We will update the memory files 
at the end of this session.
```

---

### Prompt 2 — Work

No special prompt needed. Just have your normal conversation, ask questions, assign tasks, or work on your project. The AI will use the loaded memory as context automatically.

**Tip:** If the AI seems to forget something from the memory summary, remind it:
```
Remember from our loaded memory that [remind the relevant fact].
```

---

### Prompt 3 — Capture New Memories

Send this when you are done with the main work of the session:

```
We are done with the main work for this session. 

Review everything discussed and identify:
1. New facts or knowledge that should be added to semantic memory
2. Any existing semantic memory files that need updating
3. Decisions made that should be logged
4. Any new tasks or follow-up actions for prospective memory
5. Any retrieval documents referenced that should be saved
6. Any existing memories that are now outdated and should be flagged

Propose exactly what should be written or updated. Do not write anything yet.
Present your proposals as a numbered list so I can approve or reject each one.
```

**Wait for:** A numbered proposal list from the AI.

**Respond to each proposal:**
```
Approved: [item numbers]
Rejected: [item numbers] — reason: [optional]
Edit item [number]: [your changes]
```

---

### Prompt 4 — Review Before Saving

Send after approving proposals:

```
Show me the exact content of every file you are about to write — 
full content including frontmatter for each one. 
Confirm the author: field shows [your current model name] on every file.
Present them one at a time. Do not write anything until I give final approval.
```

Review each file. For each one respond with:
```
Approved. Next file.
```
or
```
Change [this specific thing] then show me again.
```

---

### Prompt 5 — Close Session

Send after all files are approved:

```
All approved. Now close this session:

1. Write all approved items to _inbox/ with author: [your model name]
2. Create an episodic log entry for today's session in _inbox/ covering:
   - IDE and model used: [IDE name] / [model name]
   - What we worked on
   - Key decisions made
   - What worked and what didn't
   - Facts extracted for semantic memory
   - Follow-up tasks created
3. Run the lint pass — promote all _inbox/ items, check for conflicts, 
   update index.md, compress any episodic files older than 90 days
4. Update _meta/models.md with session end time, files written, and model used
5. Write SESSION_END to _meta/log.md including model name and IDE
6. Give me a closing summary: what was saved, what was archived, 
   what is pending, and any flags requiring my attention
```

**Wait for:** Closing summary. Save all updated files back to your Obsidian vault.

---

## PART 3 — Maintenance Prompts
### Use these as needed, not every session

---

### Run A Manual Lint Pass

```
Run a full lint pass on the vault now. Follow the steps in 
procedural/lint-pass.md exactly. Report:
- How many files were promoted from _inbox/
- How many files were flagged as stale
- How many conflicts were detected
- How many episodic files were compressed
- Any orphan files found
- Updated vault stats

Write the lint summary to _meta/log.md when done, including your model name.
```

---

### Review Stale Memories

```
List all files in the vault with status: stale. For each one show me:
- The filename and title
- When it was created and when its TTL expired
- Which model authored it (from the author: field)
- A one-line summary of what it contains
- Your recommendation: renew, update, or archive

Present as a table. Wait for my decision on each before making any changes.
```

---

### Switch AI Models Mid-Project

Use this when changing to a different AI model during an active project:

```
I am switching AI models for this vault.

Previous model: [old model name]
New model: [new model name]
IDE / Interface: [IDE name]

Please:
1. Confirm you have read _meta/CLAUDE.md and understand the vault rules
2. Register the new model in _meta/models.md
3. Write a REGISTER entry to _meta/log.md noting the model switch
4. Confirm your model name as you will record it in the author: field going forward
5. Load the current vault context and summarise what you know about this project

All files you create or modify going forward must use [new model name] 
in the author: field.
```

---

### Add A New AI Model To The Vault

```
I am adding a new AI model to this vault: [model name and version].
IDE / Interface it will be used from: [IDE name]

Please:
1. Add it to the registered models table in _meta/models.md
2. Write a REGISTER entry to _meta/log.md
3. Confirm it is now registered and ready to operate in this vault
```

---

### Check Model Provenance On A File

Use when you want to know which model created or last modified a specific file:

```
Check the author: field and the log entries in _meta/log.md for the file 
named [filename]. Tell me:
- Which model created it
- Which model last modified it
- When each change was made
- Which IDE was used if recorded
```

---

### Add A New Use Case

```
I want to add a new use case to this system called [use case name].

Here is the context for this use case:
[Describe what this project type is, what should be remembered, 
and what the key benefit is]

Please:
1. Propose a topic map for this use case (filenames + one-line descriptions)
2. Draft the addition to procedural/new-project-init.md
3. Draft the addition to SYSTEM-EXPLAINER.md Section 11
4. Draft a version increment and changelog entry for _meta/CLAUDE.md

Write all proposed changes to _inbox/ with author: [your model name] 
for my review. Do not update any active files until I approve.
```

---

### Request A Vault Health Report

```
Generate a vault health report covering:
1. Total files by memory type and status
2. Files expiring in the next 30 days
3. Any unresolved conflicts or orphan files
4. _inbox/ items pending promotion
5. Active prospective tasks and their deadlines
6. Last lint pass date and what it found
7. Which AI models have contributed to this vault (from models.md and author: fields)
8. Overall assessment: is the vault healthy?

Present as a structured report. Flag anything that needs my attention.
```

---

## PART 4 — Quick Reference
### One-line shortcuts once you know the system well

| Action | Short Prompt |
|---|---|
| Extract external chat | Open CHAT-EXTRACTION-PROMPT.md, paste at end of chat, check quality table, save to _inbox/ |
| Start session | "New session — I am using [model name] in [IDE]. Load memory and summarise." |
| End work, capture | "What should we save from this session?" |
| Review before save | "Show me before you write — confirm author: shows [model name]." |
| Approve and close | "Approved — write with author: [model name], lint, and close session." |
| Switch model | "Switching to [new model]. Register it and reload context." |
| Check who wrote a file | "Who authored [filename] and when?" |
| Check vault health | "Run a vault health report including model attribution summary." |
| Manual lint | "Run a full lint pass and report." |
| Add use case | "I want to add a new use case called [name]." |
| Fix wrong memory | "The following memory is wrong: [describe]. Flag it for update." |
| Find something | "Search the vault for anything related to [topic]." |

---

## Troubleshooting

**The AI seems to have forgotten the rules mid-session:**
```
Please re-read _meta/CLAUDE.md now and confirm you are still following 
the schema rules, especially the inbox-first rule and template duplication rule.
Confirm your model name is [model name] and you are recording it in author: fields.
```

**The AI used the wrong model name in the author: field:**
```
Stop. The author: field shows the wrong model name. Please correct it to 
[correct model name] on any files written this session before we continue.
```

**The AI wrote directly to a memory folder instead of _inbox/:**
```
Stop. You wrote directly to a memory folder which violates Rule 1 — Inbox First. 
Please move that content to _inbox/ with status: pending and do not write 
directly to memory folders again this session.
```

**The AI modified a TEMPLATE.md file:**
```
Stop. TEMPLATE.md files are read-only masters per Rule 4. 
Please restore the TEMPLATE.md to its original blank state and 
create a properly named duplicate file instead.
```

**You want to undo something the AI wrote:**
```
Please reverse the last write operation — move [filename] back to _inbox/ 
with status: pending and restore the previous version of any file that 
was updated. Log a REVERT action in _meta/log.md including your model name.
```

**The vault index seems out of date:**
```
Please rebuild _meta/index.md from scratch by scanning all active files 
in every memory folder and updating the content catalog and vault stats.
```

**You cannot tell which model wrote a specific memory:**
```
Scan all files in [folder name] and list the author: field value for each file.
Cross reference with _meta/log.md to confirm which model and IDE created each one.
Present as a table with filename, author, and date created.
```

---

## Registered Models In This Vault

> Keep this table updated as you add models. Copy the model name exactly as it appears here when filling in prompts.

| Model Name (for author: field) | Provider | IDE / Interface | Added |
|---|---|---|---|
| [your-model-name] | [provider] | [IDE / interface] | — |

> Add your models here when you run Step 1 of the init loop. The AI will register itself automatically.

> Add new models to this table and to `_meta/models.md` when you introduce them to the vault.

---

*End of Prompts Guide — v1.1.0*

*For full system documentation see `SYSTEM-EXPLAINER.md`*
*For the rules engine see `_meta/CLAUDE.md`*
*For individual workflow details see the `procedural/` folder*
