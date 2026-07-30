---
name: token-saver
description: >
  Keep AI sessions lean by cutting reused context, over-sized file loads,
  bloated tool definitions, and wasteful retries. Use when the user runs
  /token-saver, says "use token saver", "save tokens", "token efficient",
  "don't burn context", "keep context lean", or is about to hit rate limits
  on Claude, Codex, ChatGPT, Grok, or similar agents.
---

# Token Saver

Act as a token-efficiency co-pilot for this job. Prefer the smallest payload
and the shortest useful answer that still completes the user's request.

Core idea: **the user's latest message is usually a tiny fraction of the
request.** History, tool definitions, file dumps, and prior tool results
dominate cost. Every rule below reduces that reuse.

## When invoked

1. Briefly acknowledge that Token Saver mode is on (one short line).
2. Apply the workflow below for the remainder of the job unless the user
   disables it.
3. Prefer silent efficiency over lengthy explanations about efficiency.

Do not lecture. Do the lean thing.

## Default workflow

### 1. Scope the job

- Confirm the **single task** being solved right now.
- If the user mixes unrelated tasks, suggest splitting into a **new thread**
  after the current deliverable is done.
- Prefer carrying **artifacts** (final summary, accepted code, decision log)
  into a new thread over carrying full chat history.

### 2. Prefer search over full-file reads

Before opening a large file, PDF, log, or dump:

1. Search or list first (`grep`, ripgrep, `find`, semantic search, `ls`).
2. Open only the **matching regions** (with modest context lines).
3. Never paste an entire large file into the conversation when a snippet
  answers the question.

If the user already provided a path, still prefer targeted reads over
whole-file reads unless the file is small (rough guide: under ~200 lines
or clearly a config/spec the whole of which is needed).

### 3. Send the lightest useful source form

| Source | Prefer |
|--------|--------|
| PDF / DOCX / slides | Extracted text or Markdown |
| Image of text | OCR / extracted text when layout is irrelevant |
| Huge JSON / logs | Filtered subset, schema sample, or aggregated stats |
| Monorepo tree | Relevant package/module only |
| Prior chat | Accepted final artifact only |

Do not re-ingest binary-heavy or layout-heavy forms when plain text works.

### 4. Run exact work as code when possible

If the task is deterministic, **prefer local code/tools over model reasoning**:

- counts, sorts, renames, regex transforms
- JSON/YAML/CSV field extraction
- git blame/log/diff for facts about the repo
- tests, linters, typecheckers for verification
- database or cache lookups when an answer may already exist

Only use the model for judgment, synthesis, design, or ambiguous language.

### 5. Build the next step from the accepted result

When work is multi-stage (research → plan → implement → review):

1. Produce a **clean artifact** at the end of each stage.
2. Ask the user (or assume, if they already approved) which version is
   accepted.
3. Start the next stage from **accepted artifact + delta instructions**.
4. Do **not** drag rejected drafts, failed tool dumps, or long debate
   into the next stage unless the user explicitly needs them.

### 6. Keep answers to the length requested

- If the user specifies length (JSON only, 5 bullets, ≤150 words, patch
  only), **obey it**.
- If length is unspecified, default to the **shortest complete answer**.
- Prefer patches/diffs over full-file rewrites when editing code.
- Prefer structured bullets or tables over essays when listing facts.
- Avoid restating the question, padding with preambles, or repeating
  large code blocks the user already has.

Output costs twice: once when generated, again when reused as input.

### 7. Stop pointless retries

Before retrying a failed approach:

1. State what failed in one sentence.
2. Change **one** variable (prompt, tool, path, assumption).
3. Cap automatic retry loops (default: **2** retries on the same tactic).
4. If still stuck, stop and ask for a decision rather than burning more
   context on the same failing path.

Do not re-run the same tool call with the same arguments hoping for a
different outcome.

### 8. Load only tools the job needs

Tool/MCP definitions are paid for **before** the model acts.

- Prefer built-in shell/file tools over loading extra MCP servers for
  simple work.
- If multiple MCP servers are connected, use only those required for
  this job and avoid exploratory calls into unrelated servers.
- When advising setup (not mid-task), recommend disabling unused MCP
  servers for lean sessions.
- Never dump full tool schemas into the user-visible answer.

### 9. Stay aware of context pressure

Watch for signs of a bloated thread:

- repeated full-file dumps
- long tool result chains no longer needed
- task drift into a second unrelated goal
- user about to paste a large PDF/log "just in case"

When pressure is high:

1. Summarize the durable state into a short **handoff block**.
2. Recommend a **fresh thread** seeded with that handoff.
3. If the host supports compaction / context editing, use it and treat
   the compacted summary as approximate—re-pin critical facts explicitly.

### 10. Suggest the cheapest model that works

When the host lets the user pick a model (or the user asks):

| Work type | Bias toward |
|-----------|-------------|
| Formatting, renames, simple transforms | Small / fast model |
| Straightforward code edits with tests | Mid-tier model |
| Architecture, security, hard debugging | Stronger reasoning model |
| Bulk mechanical refactors | Script/code first; model only for edge cases |

Rule of thumb: **use the dumbest model that still does the job reliably.**
Offer the suggestion once; do not nag.

## Response shape under Token Saver

Unless the user asks for a long form:

1. Lead with the answer or change.
2. Keep rationale short and optional.
3. Put large intermediate data in files on disk, not in chat, when the
   host allows file writes.
4. When summarizing research, include only claims that affect the next
   decision.

## Handoff block template

When starting a clean thread or compacting, produce:

```markdown
## Handoff
- Goal:
- Accepted decisions:
- Current artifact paths:
- Open questions:
- Do not reopen:
```

Keep it under ~200 words unless the user needs more.

## Anti-patterns (never do these)

- "I'll read the whole repo first" without a targeted reason
- Pasting multi-thousand-line files into chat
- Continuing a failed approach more than twice without a new plan
- Carrying an entire research debate into implementation
- Asking for a 50-page report when a one-pager was requested
- Correcting a bad user prompt with a long follow-up instead of editing
  the original request (advise the user to edit-and-resend when the UI
  supports it)
- Enabling or calling every available MCP server "for completeness"

## User-facing tips (only when relevant)

Share these briefly when they would prevent immediate waste:

1. **Edit, don't argue** — fix the previous message instead of sending
   "no, that's wrong."
2. **Batch related questions** — one prompt, explicit output format.
3. **New task → new thread** — carry the artifact, not the argument.
4. **Convert heavy sources to text** before attaching.
5. **Cache answers you will reuse** in a notes file, DB, or personal
   knowledge base so the model does not re-derive them.

## Success criteria

You are doing this skill correctly when:

- Large sources are sampled, not swallowed whole
- Stage boundaries produce clean artifacts
- Answers match requested length
- Retries are rare and intentional
- The user can continue in a fresh thread with a short handoff

## References

- Full habit list: `references/fifteen-rules.md`
- Install and host notes: repository `README.md`
