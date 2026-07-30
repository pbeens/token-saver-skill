# Usage examples

Short patterns for invoking Token Saver in real sessions.

**Skill repo:** [https://github.com/pbeens/token-saver-skill](https://github.com/pbeens/token-saver-skill)

## Start of a coding task

```text
/token-saver

Fix the failing auth tests in packages/api. Search first; don't load the
whole monorepo. Prefer a minimal patch. If context gets heavy, give me a
handoff block.
```

## Research then implement (two threads)

**Thread A — research**

```text
Use token-saver. Summarize how this repo handles rate limiting in ≤200 words
with file:line citations. Output only the summary and paths.
```

**Thread B — implement** (new chat)

```text
Use token-saver. Implement the change below using only this handoff and the
cited files. Do not re-explore the whole repo.

## Handoff
- Goal: add per-IP burst limit to /api/login
- Accepted decisions: token bucket; 10/min; existing middleware pattern
- Current artifact paths: docs/rate-limit-brief.md
- Open questions: none
- Do not reopen: global CDN-level limiting
```

## Large log / PDF

```text
Use token-saver. I care about timeout errors between 14:00–14:15 only.
Search the log; quote ≤30 relevant lines; do not paste the full file.
```

## Near rate limit

```text
We're near the Claude limit. Token-saver mode: smallest useful answers,
no full-file reads, stop after two failed approaches and ask me.
```

## Model choice nudge

```text
Token-saver: is this task safe on a small/fast model, or do I need a
strong reasoning model? One paragraph.
```
