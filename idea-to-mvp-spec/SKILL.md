---
name: idea-to-mvp-spec
description: |
  Turn a raw idea (one sentence to a paragraph) into a structured, opinionated MVP spec and push it to Notion.
  Trigger on: "write a spec for", "turn this into a spec", "spec out this idea", "MVP spec", "product spec",
  "I have an idea — let's spec it", "build a spec for", "make this into an MVP plan", "write up this idea",
  or any time the user describes a product idea and wants a structured written output.
  Always use this skill proactively when the user describes a product or business idea and seems to want
  to move toward execution, even if they don't use the word "spec".
---

# idea-to-mvp-spec

Transform a raw idea into a concise, opinionated MVP spec — then push it directly to Notion.

The goal of this skill is not to produce a business plan. It's to produce the minimum structured thinking needed to **start building**: who it's for, what the core loop is, what's explicitly cut, and what week 1 looks like.

---

## Step 1 — Extract the idea

The user may provide anywhere from one sentence to a few paragraphs. Read it carefully.

If critical information is missing, ask **at most 2 questions** — one per gap. The two highest-value questions are:
1. **Who is the target user?** (specific role/context, not "developers" or "businesses")
2. **What is the core pain?** (what does the user currently do instead, and why is it bad?)

If the idea is clear enough to proceed, don't ask — just build the spec and let the user correct it.

---

## Step 2 — Write the MVP spec

Produce the spec using this exact structure. Be opinionated — don't hedge everything. If the idea is vague, make a concrete choice and flag it as an assumption.

```
# MVP Spec: <Product Name>

**Date:** <today>
**Status:** Draft

---

## Problem
<2–3 sentences. What is broken, for whom, and why existing solutions fail.
Be specific: "Freelance developers using Upwork waste 2h/day on proposal writing because..."
NOT: "There is an opportunity in the market for...">

## Target User
<One sentence. Specific role + context.>
Example: "Solo consultant billing 3–5 clients simultaneously via Notion + spreadsheets"

## Core Value Proposition
<One sentence. What does the user get that they can't easily get elsewhere?>

## MVP Feature Set (build these, nothing else)
1. <Feature — what it does, not how it's built>
2. <Feature>
3. <Feature>
(3–5 features max. If you feel the urge to add more, cut instead.)

## Explicitly Out of Scope (v1)
- <Thing that sounds important but isn't needed to validate the core>
- <Thing>
- <Thing>
(At least 3 explicit cuts. Cutting is as important as building.)

## Tech Stack
| Layer | Choice | Why |
|-------|--------|-----|
| Frontend | ... | ... |
| Backend | ... | ... |
| DB | ... | ... |
| Auth | ... | ... |
| AI/LLM | ... | ... |
(Remove rows that don't apply. Add infra/deploy row if relevant.)

## Key Risks
1. <Risk — one line, probability + impact>
2. <Risk>
3. <Risk>

## Week 1 Execution Plan
| Day | Goal |
|-----|------|
| Mon | ... |
| Tue | ... |
| Wed | ... |
| Thu | ... |
| Fri | Deployed / shown to first user |

## Success Criteria (end of week 2)
- <Measurable thing 1>
- <Measurable thing 2>
(Not "users are happy". Something you can check: "3 beta users completed the core loop without guidance")

## Assumptions to Validate
- [ ] <Assumption that could kill this if wrong>
- [ ] <Assumption>
```

---

## Step 3 — Push to Notion

Use the `notion-create-pages` tool to create the spec as a Notion page.

**Default parent:** the AI Transition Roadmap page (`35065233-3a16-8171-9cb6-d85390d4ff6c`).

If the user specifies a different location (e.g., "put it under my Ideas database" or pastes a Notion URL), fetch that page/database first using `notion-fetch` to get its ID, then use it as the parent.

Call structure:
```
notion-create-pages(
  parent={ type: "page_id", page_id: "35065233-3a16-8171-9cb6-d85390d4ff6c" },
  pages=[{
    properties: { title: "MVP Spec: <Product Name>" },
    icon: "💡",
    content: <full spec in Notion markdown>
  }]
)
```

After creation, return the Notion page URL to the user.

---

## Step 4 — Display in chat

Show the full spec inline in the conversation as well — don't just say "pushed to Notion". The user should be able to read and react to it immediately without opening a browser.

End with:
- The Notion page URL
- One sentence flagging the **single biggest assumption** the user should validate first

---

## Quality bar

A good spec:
- Is opinionated — makes concrete choices, doesn't say "we could use X or Y"
- Has a Week 1 plan that ends with something shipped or shown to a real user
- Has ≥3 explicit cuts in "Out of Scope" — showing you thought about what NOT to build
- Has a success criterion a 10-year-old could evaluate
- Fits in a single Notion page (no appendices)

A bad spec:
- Uses phrases like "leveraging AI to..." or "seamlessly integrates..."
- Has a feature list of 8+ items
- Has no out-of-scope section
- Has a Week 1 plan that ends with "more planning"
- Confuses "build" with "validate"
