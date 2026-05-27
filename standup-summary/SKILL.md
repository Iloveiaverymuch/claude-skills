---
name: standup-summary
description: |
  Generate a clean daily standup update from your MCP standup log, then save it as a dated Markdown file.
  Trigger on: "standup", "daily standup", "what did I do today", "write my standup", "generate standup",
  "log my standup", "what did I work on", "morning standup", "standup update", or any request to summarize
  recent work in yesterday/today/blockers format. Use this skill proactively when the user seems to be
  starting or ending their work day and hasn't yet run their standup.
---

# standup-summary

Generate a formatted standup from the personal MCP server's standup log, save it as a dated file, and display it in chat.

This skill works in two modes depending on what the user provides:

- **Read mode** — user has no new standup to log; just pull and format the most recent entries.
- **Write mode** — user describes what they did; log it via `log_standup`, then format and display.

Detect which mode applies from context. If the user says "write my standup" or describes their work → Write mode. If they say "show my standup" or "what's my standup" → Read mode. If unclear, ask one question: "Do you want to log new work, or just see recent entries?"

---

## Step 1 — Collect or fetch standup data

**Write mode:** ask the user for the three standup fields if not already provided:
- Yesterday: what did you finish or make progress on?
- Today: what are you working on?
- Blockers: anything in the way? (optional — default to "None")

Then call `log_standup` with these values:
```
log_standup(yesterday="...", today="...", blockers="...")
```

**Read mode:** skip directly to Step 2.

---

## Step 2 — Pull recent log entries

Call `get_standup_log(last_n=5)` to fetch recent entries. This gives context for what's been happening across the past week — useful for spotting patterns or carried-over work.

---

## Step 3 — Format the standup

Produce a clean, scannable standup in this exact format:

```
## Standup — <Day, Month DD YYYY>

**Yesterday**
<What was done — 1–4 bullets, specific and concrete>

**Today**
<What's planned — 1–4 bullets, with any relevant context>

**Blockers**
<Bullet list, or "None">

---
*Recent context (last 5 days):*
<1–2 sentence summary of trajectory — what's the arc of work this week?>
```

Rules for the format:
- Bullets should be specific ("Debugged MCP stdio scope issue" not "Did some debugging")
- Don't pad — if there's only one thing, one bullet is correct
- The "Recent context" line is a synthesis sentence, not a list — it's for the reader to orient quickly
- Keep the whole thing under 200 words

---

## Step 4 — Save to file

Save the formatted standup as a Markdown file:

- Path: `~/.ai/standups/YYYY-MM-DD_standup.md`
- Create the `~/.ai/standups/` directory if it doesn't exist
- If a file already exists for today, append to it with a separator rather than overwriting

Use the Bash tool:
```bash
mkdir -p ~/.ai/standups
```
Then use the Write tool to save the file.

---

## Step 5 — Display in chat

Print the full formatted standup in the conversation. Tell the user the file was saved and its path.

---

## Quality bar

A good standup:
- Is specific enough that someone reading it knows exactly what you worked on
- Has no filler ("various tasks", "some work on the project")
- Shows momentum — the "Recent context" line gives a sense of direction
- Takes under 30 seconds to read

A bad standup:
- Is vague
- Has empty sections padded with "N/A" or "nothing"
- Doesn't match what's actually in the log
