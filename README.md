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

### Handoff when the thread gets heavy

Ask for:

```text
Give me a token-saver handoff block so I can start a clean thread.
```

You should get something like:

```markdown
## Handoff
- Goal:
- Accepted decisions:
- Current artifact paths:
- Open questions:
- Do not reopen:
```

Paste that into a **new** chat and continue.

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
