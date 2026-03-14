# AI Memory System with Obsidian
## A guide to replicating persistent context across AI sessions

> **Who this is for:** Anyone who uses AI (Claude, Copilot, ChatGPT, etc.) regularly and wants the AI to "know you" without having to re-explain yourself every time.

---

## The problem this solves

AI assistants have no memory between sessions. Every conversation starts blank. If you have complex projects, a rich life, and well-defined preferences, this forces you to constantly repeat context — or settle for generic responses.

This system turns Obsidian into an "externalized memory" that you can inject into any AI session.

---

## General architecture

```
vault/
├── CLAUDE.md              ← Master context document (auto-loaded by Claude Code)
├── COPILOT.md             ← Identical copy for VS Code Copilot (or any other AI)
├── TASKS.md               ← Active, recurring, upcoming, and someday tasks
│
├── memory/                ← Structured memory by domain
│   ├── glossary.md        ← Acronyms, internal terms, nicknames
│   ├── people/            ← One .md per relevant person
│   │   ├── person1.md
│   │   └── person2.md
│   ├── projects/          ← One .md per active project
│   │   ├── project-a.md
│   │   └── project-b.md
│   └── context/           ← Stable structural context
│       ├── company.md     ← Professional environment, tools, processes
│       └── personality.md ← Personality profile (optional but powerful)
│
├── FolderA/
│   ├── ContextSummary.md  ← Semantic index of the folder for the AI
│   └── (regular notes)
│
├── FolderB/
│   ├── ContextSummary.md
│   └── (regular notes)
│
└── ...
```

---

## Component 1: The master document (`CLAUDE.md`)

This is the heart of the system. It loads automatically at the start of each Claude Code session (if placed in the working directory). For other AI tools, paste it at the beginning of the chat or attach it as context.

### What to include

```markdown
# Context for AI sessions

## Who I am
- Name, role, place of residence
- Current profession and specialization
- Relevant personal situation (family, projects, status)

## Intellectual profile
- How I think, what theoretical frameworks I use
- Authors/topics that influence me
- Personal concepts or frameworks

## Active projects
(references to the corresponding folders/files)

## How to interact with me
- Preferred tone (formal / peer / technical)
- Primary language
- Things I value: complexity, citations, connections across disciplines
- Things to avoid: disclaimers, oversimplification, condescension

## Tech stack and environment
- What tools I use
- Where repos live
- Project conventions

## Update protocol
(instructions to the AI about what to update at the end of each session)
```

### Golden rule of the master document

**Dense but scannable.** The AI reads it in full at the start. If it's too long, it loses relevance in longer conversations. Aim for 400–800 lines max. Anything that doesn't fit here goes in `memory/`.

---

## Component 2: The `memory/` folder

Divide memory into four types of files:

### `glossary.md` — Internal vocabulary

```markdown
# Glossary

## Acronyms
| Term  | Meaning       | Context                        |
|-------|---------------|-------------------------------|
| PX    | [Project X]   | Short name for main project   |
| vault | Obsidian repo | My personal knowledge base    |

## Internal terms
| Term        | Meaning                        |
|-------------|-------------------------------|
| [code name] | [what it means in your context] |

## Nicknames → Full names
| Nickname | Person                  |
|----------|------------------------|
| [nick]   | [real name, relationship] |
```

**Why it works:** When you later say "what's the status of [nickname] and [project]", the AI already knows what you mean without you having to explain.

---

### `people/[name].md` — People profiles

One file per relevant person (partner, children, collaborators, frequent clients, etc.).

```markdown
# Full Name

**Also known as:** [nickname]
**Relationship:** [relationship to you]
**Context:** [what they do, where they live, etc.]

## Details relevant for the AI
- Shared interests
- Personality / communication style
- Important notes for interaction

## Shared projects or topics
- [list of joint projects]
```

**What NOT to include:** Sensitive data, passwords, legal documents. Only what helps the AI contextualize mentions of that person.

**Optional but powerful:** If you've done personality tests (MBTI, Big Five, HEXACO), adding a profile summary helps the AI understand relational dynamics significantly better.

---

### `projects/[project].md` — Active projects

```markdown
# Project Name

**Codename:** [short name for use in conversations]
**Status:** Active / On hold / Completed
**Repo / Location:** [where the code or files live]
**Stack:** [technologies, tools]

## What it is
[description in 2–3 sentences]

## Current status
[progress, last milestone, next steps]

## Notes for the AI
- Project conventions
- Relevant architecture decisions
- What it should not touch / change
```

---

### `context/company.md` — Professional environment

```markdown
# Professional context

## Tools and systems
| Tool   | Use          | Notes  |
|--------|-------------|--------|
| [tool] | [what for]  | [notes] |

## Current role
- Company, position, specialization
- Type of clients / projects

## Recurring processes
| Process  | What it means in practice |
|----------|--------------------------|
| [name]   | [description]            |
```

---

### `context/personality.md` — Personality profile (optional)

This is the most powerful and most personal file. If you've done a personality test (HEXACO is recommended for its scientific validity), document the results with applied interpretations:

```markdown
# Personality profile

**Test:** [test name]
**Date:** [when you took it]

## Main traits

### [Trait 1] — [score / level]
[interpretation in your specific context]

### [Trait 2] — [score / level]
[interpretation]

...

## Implications for AI interaction
- [list of how the AI should adapt its behavior]
```

**Why this is useful:** The AI can calibrate how much validation it offers, how it frames criticism, whether to ask questions or give direct answers, and whether to assume you need emotional support or not.

---

## Component 3: `ContextSummary.md` in each folder

Each vault folder has a file that acts as a **semantic index** for the AI.

```markdown
# Context Summary — [Folder Name]

This folder contains [brief description]. [How it fits in your workflow].

## What's here

### [File or subfolder 1]
[Description of what it contains and what it's for]

### [File or subfolder 2]
[Description]

...

## Current status
[Progress, last relevant change, notes for the AI]
```

**When to use it:** When you ask the AI to work on a specific folder ("review my research notes on X"), point it to the `ContextSummary.md` first. It doesn't need to read every file.

---

## Component 4: `TASKS.md`

A task list structured by time horizon. Recommended format:

```markdown
## Active
- [ ] **[Name]** — brief description

## Recurring
- [ ] **[Name]** — description — due [date] 🔁 [frequency]

## Upcoming
- [ ] **[Name]** — due [date]

## Someday
- [ ] [idea or pending project with no date]
```

**AI use:** "Review my TASKS.md and tell me what I have due this week" works perfectly when you have explicit dates.

---

## Component 5: Wikilinks and the knowledge graph

Obsidian's `[[wikilinks]]` are not just a navigation convenience — they are the connective tissue that turns a collection of files into a **navigable knowledge graph**. Both you and the AI benefit from explicit links between notes.

### The `## Links relacionados` pattern

At the end of each content note, add a section with links to related notes:

```markdown
## Links relacionados

- [[Note A]] — why this note connects
- [[Note B]] — what the relationship is
- [[Note C]] — the specific angle of relevance
```

The brief annotation after each link is important. It tells both you and the AI *why* the connection exists, not just *that* it exists. A link without context is noise; a link with a one-line reason is signal.

### What to link

- **Thematic connections** — notes that explore the same idea from different angles
- **Cross-folder bridges** — these are the most valuable: a research note linking to a creative writing chapter, a philosophy essay linking to a character profile
- **Source → application** — research that feeds into a project, a concept that informs a decision

### Let the AI build the graph for you

One of the most powerful uses of this system is asking the AI to **audit your vault for missing connections**. A prompt like:

> "Review all my vault files and find relationships between documents that exist but aren't linked. Create the wikilinks so I can see them in Obsidian's graph view."

The AI can read every file, identify thematic overlaps you missed, and add hundreds of links in one session. This is particularly effective for vaults that have grown organically over time — the connections are there, they just haven't been made explicit.

### Graph view as a thinking tool

Once the links exist, Obsidian's graph view becomes a map of your intellectual landscape. Clusters reveal where your thinking is dense. Isolated nodes reveal notes that should be connected but aren't. Bridge nodes — notes that connect otherwise separate clusters — reveal your most integrative ideas.

---

## How it integrates with AI tools

### Claude Code (CLI)
Place `CLAUDE.md` in the project's working directory. Claude loads it automatically at the start of every session. For coding projects, this is the most seamless integration.

### VS Code + GitHub Copilot
Use `COPILOT.md` (a copy of `CLAUDE.md`) in the root directory. Copilot can include it as workspace context.

### Claude.ai / ChatGPT (web)
Create a **custom system instruction** (in Settings → Custom Instructions or equivalent) using a condensed version of the master document. For intensive sessions, paste the full `CLAUDE.md` at the start of the chat.

### Any AI with file access
Attach `CLAUDE.md` + the `ContextSummary.md` of the relevant folder + the corresponding project file.

---

## Update protocol (instructions for the AI)

Include this at the end of your `CLAUDE.md`:

```markdown
## Update protocol

Mandatory rule: at the end of each relevant session, update:

1. **CLAUDE.md** — if anything changes about my profile, projects, or preferences
2. **ContextSummary.md** of the affected folder — reflect changes made
3. **memory/** — update people or project profiles if applicable
4. **TASKS.md** — mark completed tasks or add new ones
```

This turns the AI into a co-maintainer of the system. At the end of any working session you can ask: "Update the relevant memory files with what we did today."

### Periodic maintenance: let the AI audit the vault

Beyond session-by-session updates, schedule occasional full-vault reviews. Ask the AI to:

- **Find missing wikilinks** — connections between notes that should exist but don't
- **Update `ContextSummary.md` files** — especially after adding new notes to a folder
- **Flag stale information** — projects marked "active" that haven't been touched in months
- **Identify orphan notes** — files with no incoming or outgoing links

This kind of structural maintenance is tedious for humans but trivial for an AI with file access. A single session can add hundreds of links and bring every summary file up to date.

---

## Typical workflow

**At the start of a session:**
1. The AI loads `CLAUDE.md` automatically (or you attach it)
2. If the work is on a specific folder, point to its `ContextSummary.md`
3. If the work involves specific people or projects, point to the relevant files in `memory/`

**During the session:**
- The AI operates with full context
- You can refer to projects, people, and terms by their short names

**At the end of the session:**
- Ask the AI to update the relevant files
- Or do it manually if you prefer full control

---

## Implementation recommendations

### Start simple
Don't try to build the whole system at once. Recommended order:
1. Write a basic `CLAUDE.md` (who you are, your projects, how you want to be addressed)
2. Add `TASKS.md`
3. Add `glossary.md`
4. Add project files one by one as you use them
5. Add people profiles as they become relevant
6. Add `ContextSummary.md` files in folders as needed

### Design principles
- **Each file must be readable independently** — don't rely on the AI remembering another file from the same session
- **Be specific, not exhaustive** — "use direct responses without disclaimers" beats three paragraphs about your communication philosophy
- **Absolute dates, not relative** — "due 2026-04-15" instead of "in two weeks"
- **Consistent codenames** — using the same name across all files makes it easier for the AI to connect references

### What NOT to put in the memory system
- Passwords or credentials (even if they exist elsewhere in plain text, don't reference them here)
- Information that changes very frequently (use `TASKS.md` with dates instead)
- Code or architecture decisions derivable from the code itself
- Git history or who changed what (use `git log` for that)

### A note on security

Your vault will inevitably contain personal information — names, relationships, professional context, personality traits. That's the point. But draw a hard line:

- **Never store passwords, API keys, tokens, or credentials** in any memory file. Not even "for convenience." Use a password manager (Bitwarden, 1Password, etc.) instead.
- **Never store financial account numbers, government IDs, or legal documents** in files that the AI reads. If you need these in your vault for personal reference, keep them in a separate folder that is explicitly excluded from AI context.
- **Be aware of what you share.** When you paste `CLAUDE.md` into a web AI session, everything in that file is sent to a third-party server. Write it assuming it could be read by someone other than you.
- **If your vault syncs to the cloud** (iCloud, Syncthing, etc.), ensure the sync service is one you trust. Plain Markdown files are readable by anyone with access to the storage.

The rule of thumb: if losing access to a piece of information would cause you financial, legal, or personal harm, it doesn't belong in the memory system.

---

## Compatible tools

| Tool                    | Compatibility | Method                              |
|-------------------------|--------------|-------------------------------------|
| Claude Code (CLI)       | Native       | CLAUDE.md in working directory      |
| VS Code Copilot         | High         | COPILOT.md + context files          |
| Claude.ai               | Manual       | Attach or paste at start            |
| ChatGPT                 | Manual       | Custom Instructions + attach        |
| Cursor                  | High         | .cursorrules or context files       |
| Obsidian + AI plugins   | Native       | Inside the vault                    |

---

## Why Obsidian and not something else

- **Plain Markdown files** — no vendor lock-in, work in any editor
- **Sync via iCloud / Syncthing / etc.** — available on all your devices
- **Human-readable vault** — it's not just for the AI, it's your second brain
- **Graph view** — visualize connections between notes
- **Plugins** — direct AI integration if you want to go further (Smart Connections, etc.)

The closest alternative is Notion, but as a proprietary database it's significantly harder to inject as context into AI tools in a fluid way.

---

## How the system evolves

This system grows organically. It's not a one-time setup. Over time:

- `ContextSummary.md` files get enriched with cross-folder analysis
- People profiles gain more nuance
- The glossary grows with terms you use repeatedly with the AI
- The master document reflects who you are now, not who you were when you wrote it

The signal that the system is working: when you open a session with "continue with [project]" and the AI picks up exactly where you left off, with the right tone, without asking who you are or what you want.

---

*System developed and refined through real-world use with Claude Code. Last updated: March 2026.*
