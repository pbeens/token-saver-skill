# Token Saver

**Repository:** [github.com/pbeens/token-saver-skill](https://github.com/pbeens/token-saver-skill)

> **Status: draft / in development — do not trust this yet.**
>
> This repository is an early work in progress. Skill text, install paths,
> docs, and URLs may change without notice. Treat everything here as
> experimental until a stable release is announced. Not production-ready.

**A portable agent skill that keeps Claude Code, Codex, Grok, and similar
coding agents from burning your context window on reused history, whole-file
dumps, and pointless retries.**

Inspired by public guidance on *reused input* and context hygiene (notably
Nate B. Jones’s talk
[Paste This Into Claude, Never Hit a Token Limit Again](https://www.youtube.com/watch?v=Y8vAQ1FgNbM)).
That talk promises a **Token Saver** skill; this repo is an open, equivalent
implementation you can install and share.

> Core idea: the message you type is usually a tiny part of the bill.
> History, tool definitions, and prior tool results dominate. Token Saver
> makes the agent behave as if a clean desk is part of the job.

---

## What you get

| Path | Role |
|------|------|
| `skills/token-saver/SKILL.md` | The skill agents actually load |
| `skills/token-saver/agents/openai.yaml` | Codex-friendly display metadata |
| `skills/token-saver/references/fifteen-rules.md` | Full Level 1–3 habit checklist |
| `AGENTS.md` | Project agent profile for this repo |
| `README.md` | Install + usage (this file) |

The skill tells the agent to:

1. **Search before** opening large sources  
2. **Send passages**, not whole files  
3. **Run exact work as code** when judgment is not required  
4. **Carry accepted artifacts** into the next stage (not the whole debate)  
5. **Match requested output length**  
6. **Stop pointless retries** (change one variable; cap loops)  
7. **Use only tools the job needs** (MCP definitions are expensive)  
8. **Watch context pressure** and emit a short handoff for a fresh thread  
9. **Suggest the cheapest model** that still works  

It also nudges Level-1 human habits: edit-and-resend instead of arguing,
batch related questions, new task → new thread, light source formats, and
caching reusable answers.

---

## Quick start

### Get the skill

```bash
git clone https://github.com/pbeens/token-saver-skill.git
cd token-saver-skill
```

Or download a ZIP from
[github.com/pbeens/token-saver-skill](https://github.com/pbeens/token-saver-skill).

### Option A — Project skill (share with a repo)

Copy the skill into your project:

```bash
# from this repo
cp -R skills/token-saver /path/to/your-project/.claude/skills/token-saver
# or for Grok Build
cp -R skills/token-saver /path/to/your-project/.grok/skills/token-saver
# or for Codex-style project skills (path may vary by version)
cp -R skills/token-saver /path/to/your-project/.agents/skills/token-saver
```

Commit the skill so teammates get the same behavior.

### Option B — User-global skill (all projects)

```bash
# Claude Code (typical user skills location)
mkdir -p ~/.claude/skills
cp -R skills/token-saver ~/.claude/skills/token-saver

# Grok Build
mkdir -p ~/.grok/skills
cp -R skills/token-saver ~/.grok/skills/token-saver

# Shared agents profile layout
mkdir -p ~/.agents/skills
cp -R skills/token-saver ~/.agents/skills/token-saver

# Codex (if your install uses ~/.codex/skills)
mkdir -p ~/.codex/skills
cp -R skills/token-saver ~/.codex/skills/token-saver
```

Exact skill directories differ slightly by host and version. If a host
only lists skills under a different path, place `token-saver/SKILL.md`
there with the same folder shape.

### Option C — Work inside this repo

```bash
git clone https://github.com/pbeens/token-saver-skill.git
cd token-saver-skill
```

Open the folder in your agent host. Point the host at `AGENTS.md` if it
supports a project agents file.

---

## Best strategy

Token Saver works best when **you** set up a clean job and the skill keeps
the agent from re-dirtying the desk. Use this pattern:

### 1. One task, one thread

Pick a single goal for the chat. When the goal changes (research →
implement, bug A → bug B), start a **new thread**. Carry only the accepted
artifact or a short handoff—not the whole debate.

### 2. Invoke at the start of hard jobs

Turn the skill on **before** the agent loads large sources, not after the
window is already full:

```text
/token-saver

Fix the failing auth tests in packages/api. Search first; don't load the
whole monorepo. Prefer a minimal patch. If context gets heavy, give me a
handoff block.
```

For routine one-liners you can skip it. For monorepos, logs, multi-stage
work, or near rate limits, invoke it first.

### 3. Name the deliverable and length

State the output shape up front. Output is billed when generated **and**
again when reused as input on later turns.

| You need | Say |
|----------|-----|
| Decisions only | “≤150 words; file:line citations; no essay” |
| Code change | “Minimal patch / diff only” |
| Structured data | “JSON only matching this schema…” |
| Log triage | “≤30 relevant lines; no full file” |

### 4. Stage multi-step work

Split research and implementation into two threads:

1. **Thread A — research:** lean summary + paths/citations only.  
2. Accept the brief (or save it to a file).  
3. **Thread B — implement:** paste handoff + brief; forbid re-exploring
   the whole repo.

That single habit usually beats shaving words off prompts.

### 5. Prefer light sources and search

- Convert PDFs/slides to text when layout does not matter.  
- Point at paths and let the agent **search, then open slices**—do not
  paste multi-thousand-line dumps “just in case.”  
- Keep reusable answers in a notes file or project doc so later sessions
  retrieve instead of re-deriving.

### 6. Edit, don’t argue

If the prompt was wrong, **edit and resend** the original message when the
UI allows it. A “no, that’s wrong” follow-up keeps the bad turn and all
prior tokens in the bill.

### 7. Prune tools for lean sessions

MCP/tool definitions cost tokens **before** the model acts. For focused
work, disconnect unused servers (GitHub, Slack, Sentry, Grafana, etc.)
and leave only what the job needs. Prefer built-in shell/file tools when
they are enough.

### 8. Handoff when the thread gets heavy

When the chat fills with tool dumps, rejected drafts, or task drift, ask:

```text
Give me a token-saver handoff block so I can start a clean thread.
```

Expect something like:

```markdown
## Handoff
- Goal:
- Accepted decisions:
- Current artifact paths:
- Open questions:
- Do not reopen:
```

Paste that into a **new** chat and continue.

### 9. Match model size to the work

Use a small/fast model for formatting and simple transforms; reserve
stronger models for architecture, security, and hard debugging. Ask the
skill once if you are unsure—do not thrash models mid-task without a
reason.

### 10. What “saved” looks like (and what it doesn’t)

The skill does **not** report a live “tokens saved” counter. Judge success
by behavior and host usage:

- Search-then-slice instead of whole-file reads  
- Short answers that match what you asked for  
- Stage boundaries with clean artifacts  
- Few pointless retries  
- Fresh threads seeded with handoffs  

To compare, run the **same task** with and without lean habits and check
your host’s usage meter or API token counts. Real savings depend on host,
model, tools, and habits—not a fixed multiplier.

More copy-paste patterns: [`docs/usage-examples.md`](docs/usage-examples.md).

---

## How to use it

### Slash / menu

Once installed, invoke by name:

```text
/token-saver
```

Or from a skills menu: pick **Token Saver**.

### Natural language

Any of these should match the skill description:

```text
Use the token saver skill for this job.
Keep this session token-efficient.
Don't burn context — search before reading whole files.
We're near the rate limit; work lean.
```

### One-liner you can paste at the start of a hard job

```text
Use token-saver for this task. Search before full-file reads, prefer
snippets and accepted artifacts over chat history, keep answers short,
avoid retry loops, and tell me if we should start a fresh thread with a
handoff block.
```

### What good behavior looks like

| Instead of… | Token Saver does… |
|-------------|-------------------|
| Reading an entire 3k-line log | `grep` / search, then open matching slices |
| Carrying 40 turns of research into coding | Saves a short accepted brief, continues from that |
| Writing a 2,000-word essay you did not ask for | Matches length (bullets, JSON, patch) |
| Re-running the same failing tool call | Changes one variable; stops after two retries |
| Leaving every MCP server connected “just in case” | Uses only tools needed; recommends pruning |

---

## Levels of token control (context)

This skill is **Level 2** in a simple three-level model:

| Level | Metaphor | What it is |
|-------|----------|------------|
| **1** | Clean your own desk | Manual habits (edit prompts, new threads, light sources) |
| **2** | Someone cleans the desk | This skill — agent-side automation while you work |
| **3** | Desk never gets messy | Local intermediary between client and provider (hard limits, cache hits, zero-token local answers) |

A skill **cannot** shrink the request envelope it already arrived in
(system prompt, prior turns, tool definitions already attached). Level 3
proxies address that; they are intentionally out of scope for this repo’s
runtime, but the reference doc explains the idea.

Full checklist: [`skills/token-saver/references/fifteen-rules.md`](skills/token-saver/references/fifteen-rules.md).

---

## Compatibility

Designed as a **portable Markdown skill**:

- YAML frontmatter: `name` + `description` (description drives auto-invocation)
- Instruction body the agent follows mid-task
- Optional `agents/openai.yaml` for Codex UI labels

Works with hosts that load skills from disk (Claude Code, Codex, Grok Build,
and compatible “agent skills” layouts). No API key and no install script
required beyond copying files.

---

## Project layout

```text
.
├── AGENTS.md
├── LICENSE
├── README.md
└── skills/
    └── token-saver/
        ├── SKILL.md
        ├── agents/
        │   └── openai.yaml
        └── references/
            └── fifteen-rules.md
```

---

## Contributing

Source and issues:
[github.com/pbeens/token-saver-skill](https://github.com/pbeens/token-saver-skill).

Improvements welcome, especially:

- Host-specific install notes that stay accurate over time  
- Shorter, clearer skill instructions that still change agent behavior  
- Small examples (before/after transcripts) under `docs/`  
- Optional helper scripts only when a real CLI beats pure instructions  

Please keep `SKILL.md` focused and actionable. It is a **prompt for the
agent**, not a blog post.

---

## Disclaimer

- **Draft / in development.** Do not rely on this skill or its docs as
  finished or trustworthy until a stable release is announced.  
- Independent open reconstruction of a “Token Saver” style skill.  
- Not affiliated with or endorsed by Nate B. Jones, Anthropic, OpenAI, or xAI.  
- Token savings depend on your host, model, tool setup, and habits.  
- Provider rate limits and pricing change; treat numbers in third-party
  talks as illustrative, not guarantees.

---

## License

MIT — see [`LICENSE`](LICENSE).
