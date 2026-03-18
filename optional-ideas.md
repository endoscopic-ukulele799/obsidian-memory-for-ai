# Optional Ideas

> Extras you can add to your memory system. None of these are required — think of them as house rules for a board game. Pick what fits your workflow, ignore the rest.

---

## 🔭 Horizon Strip — Visual task + event timeline at session start

**What it does:** Generates a compact visual timeline at the beginning of every memory load, showing your upcoming tasks *and* time-blocked events (trips, deadlines, meetings) sorted by date. You see at a glance what's urgent and when you're actually available.

**Why it's useful:** A flat task list hides availability. If you have 4 tasks due "next week" but you're traveling all week, that context is invisible. The horizon strip interleaves tasks with time blocks so the AI (and you) can gauge real urgency.

**Example output:**
```
📋 HORIZON — 18 Mar 2026
──────────────────────────────────────────────────
🔴  1d   📌 Dentist appointment · 19/3 10:00
🟠  2d   🚗 Weekend trip to coast (20–22) ······
🟡  4d   📌 Call accountant · 22/3
🟡  5d   ✈️ Work trip Brussels (23–25) ··········
            📌 Submit expense report · 23/3
🟡  8d   📌 Renew passport · 26/3
🟢  9d   🚗 Easter road trip (27/3–6/4) ········
            📌 Pay rent · 1/4
🟢 20d   ✈️ Family visit Lima (7–15/4) ·········
            📌 Schedule meeting · 10/4
──────────────────────────────────────────────────
          +3 future · 2 someday
```

**Color scheme:**
| Emoji | Days until event | Meaning |
|-------|-----------------|---------|
| 🔴 | 0–2 days | Immediate |
| 🟠 | 3–4 days | This week |
| 🟡 | 5–9 days | Next week |
| 🟢 | 10+ days | Comfortable |

**Design rules:**
- **Tasks** appear with 📌 and their due date
- **Trips/events** appear as context blocks with transport emoji (🚗 car, ✈️ flight, 🏢 office) and trailing dots `···` to signal duration
- **Tasks during a trip** are indented under the trip — this shows what you need to handle while traveling
- **Tasks outside trips** stay at root level — these are your "free day" tasks
- Only show items within the next ~30 days
- Items beyond 30 days and "someday" items are collapsed into a count line

**How to implement:**

1. **Create a travel/events file** (e.g., `memory/travel.md` or `memory/events.md`) that lists upcoming time blocks with date ranges:
   ```markdown
   ## Coast weekend
   - Dates: 20–22 March 2026
   - Transport: car
   
   ## Work trip Brussels
   - Dates: 23–25 March 2026
   - Transport: flight
   ```

2. **Modify your memory-load command** to also read `TASKS.md` + your events file, then instruct the AI to generate the horizon strip by:
   - Parsing all dated items from both sources
   - Calculating days-until for each
   - Sorting chronologically
   - Nesting tasks under overlapping trips
   - Applying the color scheme
   - Collapsing items beyond 30 days

3. **Add it to your load report** — place the horizon strip at the top of the memory-load output, before the context summary.

**Customization ideas:**
- Add more event types: 🏥 medical, 🎓 school, 🎉 social
- Change color thresholds to match your planning style
- Add recurring events from a `recurring.md` file
- Include a "free days" count: days in the next 30 not covered by any trip

---

## 📱 Memory Shortcut — Query your memory from your phone

**What it does:** A Cloudflare Worker that lets you query (and update) your Obsidian memory system from your phone using iOS Shortcuts, Android Tasker, or any HTTP client. Ask a question in natural language, get an answer grounded in your memory files — no apps to install.

**Why it's useful:** Your memory system lives on your computer, but questions pop up everywhere. "What do I have this week?", "When is that appointment?", "What was the decision about X?" — now you can ask from your phone and get an answer in seconds, even hands-free via Siri.

**Architecture:**
```
Phone (Shortcut / Tasker / HTTP)
    │
    │  POST /query   { "q": "What do I have this week?" }
    ▼
Cloudflare Worker (free tier)
    │
    ├─► GitHub API → reads .md files from a private repo
    │   (Tier 1 always, Tier 2 based on keywords in the question)
    │
    └─► Claude API → generates answer with loaded context
    │
    ▼
JSON response { "answer": "..." }
```

**How it works:**

1. **Your memory files live in a private GitHub repo.** You can either:
   - Initialize git directly in your vault with a `.gitignore` that only tracks `memory/` and your task file
   - Use a separate repo and sync periodically

2. **A Cloudflare Worker** receives questions and:
   - **Always loads Tier 1** files (glossary, personality, company context, working context)
   - **Detects keywords** in the question to load Tier 2 files:
     - People's names → their profile from `people/`
     - Project names → their file from `projects/`
     - Task-related words ("this week", "tasks", "appointments") → your task file
     - Decision-related words ("why did we", "when did we decide") → your decisions timeline
   - Sends the loaded context + question to Claude API
   - Returns a clean plain-text answer (Markdown stripped for voice assistants)

3. **Optionally, a `/update` endpoint** lets you modify memory from your phone:
   - "Add task: buy birthday gift for Maria, April 20" → updates your task file
   - "Note on Maria: prefers dark chocolate" → appends to the person's file
   - Safeguards: only whitelisted files are editable, content validation prevents corruption

**Setup overview:**

1. Push your memory files to a private GitHub repo
2. Deploy the Worker to Cloudflare (free tier is sufficient)
3. Set three secrets: `MEMORY_TOKEN` (random string you generate), `CLAUDE_API_KEY`, `GITHUB_TOKEN` (read-only PAT)
4. Create a Shortcut on your phone:
   - Ask for input → POST to Worker URL with auth header → Show result

**iOS Shortcut steps (Spanish UI: Atajos):**

| Step | Action | Config |
|------|--------|--------|
| 1 | Ask for Input | Prompt: "What do you want to ask?" |
| 2 | URL | `https://your-worker.workers.dev/query` |
| 3 | Get Contents of URL | POST, headers: `Content-Type: application/json` + `Authorization: Bearer <token>`, body JSON: key `q` → Provided Input |
| 4 | Get Dictionary Value | Key: `answer` |
| 5 | Show Result | Dictionary Value |

For voice mode: replace step 1 with **Dictate Text** and step 5 with **Speak Text**.

**Keeping memory in sync:**

If your vault is on a different machine than where you edit, you need sync between vault ↔ GitHub:
- **Simplest:** Initialize git in your vault root with a selective `.gitignore`. A cron job or launchd agent runs `git pull && git add -A && git commit && git push` every 30 minutes
- **Alternative:** Use Obsidian Git plugin if your vault is already a git repo
- Phone updates (via `/update`) arrive at the vault on the next pull cycle

**Keyword detection for Tier 2 loading:**

Map keywords to files in your Worker code. Example:

```javascript
const PEOPLE_MAP = {
  'maria': 'memory/people/maria.md',
  'john':  'memory/people/john.md',
};

const TASK_KEYWORDS = ['week', 'tasks', 'todo', 'pending', 'appointment'];
const DECISION_KEYWORDS = ['decided', 'decision', 'why did', 'when did'];
```

**Update endpoint safeguards:**
- Whitelist of editable files (e.g., only tasks and people files)
- People files: append-only (never rewrite)
- Task file: rewrite with Claude, but abort if result is <50% of original length
- Empty content is rejected

**Cost:** Effectively free for personal use — Cloudflare Workers free tier (100K requests/day), GitHub API (5K requests/hour), Claude Haiku (~$0.001 per query).

**Customization ideas:**
- Add a `/status` endpoint that returns your horizon strip or weekly summary without a question
- Cache Tier 1 files in Cloudflare KV with a 1-hour TTL to reduce GitHub API calls
- Add more action verbs for better task detection ("buy", "cancel", "book", "call")
- Support multiple languages in keyword detection
- Add a confirmation step for `/update` — return the proposed change and require a second call to confirm

---

*Have an idea for this list? Open an issue or PR on [obsidian-memory-for-ai](https://github.com/jrcruciani/obsidian-memory-for-ai).*
