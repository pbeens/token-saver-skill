# Fifteen Token Rules

Operational checklist behind the Token Saver skill. Inspired by public
guidance on reused input, context hygiene, and intermediary filtering
(e.g. Nate B. Jones, "Paste This Into Claude, Never Hit a Token Limit Again").

These rules are habits and system designs. The skill automates what it can
**inside** a running agent session (Level 2). Hard intercepts before the
provider (Level 3) need a local proxy such as Ringer—not this skill alone.

## Level 1 — Clean your own desk (manual habits)

### 1. Edit mistakes instead of arguing with them

If the prompt was wrong or unclear, edit and resend the original message.
Do not append "no, that's wrong" as a new turn. A correction message
preserves the bad turn and all prior tokens.

### 2. Ask related questions together; name the output shape

Batch questions that share the same sources. State the deliverable up
front: one-pager, 150 words, five bullets, JSON schema, patch only.

### 3. Start a clean task when the job changes

Long threads are great for one problem and expensive for the next.
When the goal changes, open a new thread. Keep the old thread for
history; do not make the new job carry it.

### 4. Carry the answer, not the argument

After research or drafting, forward only the **accepted artifact** into
the next stage—not failed drafts, rejected sources, or full critique
chains.

### 5. Ask only for the answer you need

Output is billed when generated and again when reused as input on later
turns. Prefer paragraphs, bullets, or JSON over sprawling reports when
a short form is enough.

### 6. Search the file yourself when you can

Do not make the model read an entire file if you can isolate the relevant
passages first. Hand the model snippets with enough context to act.

### 7. Send the lightest useful form of the source

Prefer Markdown/text over PDF and screenshots when layout is irrelevant.
Convert heavy formats before attaching them.

### 8. Keep reusable answers somewhere findable

Store accepted answers in a notes vault, OpenBrain-style DB, or project
doc so later sessions retrieve instead of re-deriving.

### 9. (Habit stack) Treat the context window like a desk

Capability without cleanup still hits a wall. More tools and longer
histories increase cleanup burden. Own the desk.

## Level 2 — Automated cleanup (what the skill does)

### 10. Load only the tools the job can use

Every connected tool ships a description into the model input. Multiple
MCP servers (GitHub, Slack, Sentry, Grafana, etc.) can burn tens of
thousands of tokens **before** the first user sentence is handled.
Connect what you need for the session; disconnect the rest.

### 11. Use native compaction / context editing on long jobs

When you cannot restart a thread mid-task:

- OpenAI-style **compaction** carries forward compressed state.
- Anthropic-style **context editing** can clear old tool results and
  thinking blocks.

These are approximations. Re-pin critical facts after compacting.

### 12. Prefer the cheapest model that still works

Serious work needs strong models. Formatting, extraction, and simple
transforms often do not. Default to the least expensive model that is
reliable for the task.

### 13. Know when prompt caching matters

Prompt caching is mainly an **API** concern for repeated stable prefixes.
Everyday chat users should focus on rules 1–12 first. API builders should
cache stable system prompts, tool defs, and large static documents.

## Level 3 — Intermediary filtering (beyond a skill)

### 14. Enforce hard packet limits via a local intermediary

A skill cannot shrink the envelope it already arrived in. A local proxy
(e.g. Ringer-style multi-agent framework) sits between client and
provider and can:

- answer from a local recipe or cache with **zero** model tokens
- select only useful passages
- forward under hard size limits
- block oversized requests entirely

### 15. Resolve known answers before calling a model

If OpenBrain / a local DB already has an accepted answer, return it
(or confirm it with the user) instead of paying for a full completion.

## Quick map: skill vs human vs proxy

| Rule | Who enforces it |
|------|-----------------|
| 1–9 | User habits + skill nudges |
| 10–12 | Skill + user session setup |
| 13 | API / app builders |
| 14–15 | Local intermediary (not SKILL.md alone) |

## Measurement intuition

Reused input often dominates total tokens. Audits in heavy agent
workspaces commonly show the vast majority of volume as **reused**
history and tool payload, not freshly typed prompts. Reducing reuse
is usually a larger win than shaving a few words off the user message.
