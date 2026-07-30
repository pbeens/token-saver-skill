# AGENTS

Shared agent profile for the **token-saver** project.

**Repository:** [https://github.com/pbeens/token-saver-skill](https://github.com/pbeens/token-saver-skill)

Install or clone this repository, then point your agent host at this file
(or copy the skill into your personal skills directory). See `README.md`
for host-specific install paths.

```bash
git clone https://github.com/pbeens/token-saver-skill.git
cd token-saver-skill
```

## Identity

- **Name:** `token-saver`
- **Purpose:** Help humans and coding agents finish work with far less
  reused context, fewer full-file loads, tighter outputs, and smarter
  model/tool choices.
- **Default stance:** Concise, practical, artifact-first. Prefer the
  smallest payload that still completes the task.

## Primary skill

When this repo is the active project, prefer the skill at:

- `skills/token-saver/SKILL.md`

Invoke it when the user asks for token efficiency, context hygiene, or
`/token-saver`, and whenever a job is about to pull large sources or
run a long multi-stage workflow.

## Project rules

1. **Do not bloat the repo with generated dumps.** Keep sample data small.
2. **Skill body is the source of truth** for agent behavior. Docs explain;
   `SKILL.md` instructs.
3. **Preserve cross-host compatibility.** The skill must remain plain
   Markdown with YAML frontmatter so Claude Code, Codex, Grok, and
   similar hosts can load it.
4. **No network calls required at runtime.** The skill is procedural
   guidance, not a SaaS dependency.
5. **When changing skill behavior,** update in this order:
   - `skills/token-saver/SKILL.md`
   - `skills/token-saver/references/fifteen-rules.md` (if habits change)
   - `README.md` usage section
   - this `AGENTS.md` if project-level policy changes

## Working style for agents in this repo

- Search before opening large files in this repo too.
- Prefer editing existing docs over adding parallel copies.
- Keep examples short and copy-pasteable.
- Do not invent private Substack download links or claim official
  affiliation with third-party creators unless cited and verified.

## Attribution

This project reconstructs a practical **Token Saver**-style skill from
publicly described habits and agent skill patterns. It is an independent
implementation intended for open sharing, not a dump of any paywalled
bundle.

## Out of scope

- Building a full Level-3 local proxy (Ringer-class intermediary)
- Guaranteeing provider-side prompt cache hit rates
- Replacing the user's MCP/server configuration automatically across
  machines

Agents may **document** those topics and point to hooks, but should not
pretend this skill alone enforces hard pre-request packet limits.
