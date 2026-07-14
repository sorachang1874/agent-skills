# agent-skills

Shared, battle-tested skills for AI coding agents (Codex CLI / ChatGPT Desktop, Claude Code, and
compatible agent harnesses). Each skill is a self-contained folder with a `SKILL.md` entry point
plus optional `references/` and `agents/` companions, distilled from real multi-agent engineering
workflows rather than written speculatively.

## Skills

| Skill | What it does |
|---|---|
| [`consult-chatgpt-pro`](skills/consult-chatgpt-pro/SKILL.md) | Use ChatGPT Pro (Chat surface, GPT-5.6 Sol) as a high-judgment **advisory** consultant for consequential planning, architecture decisions, approach reviews, hard debugging, and milestone audits — with a strict consultation contract: scope gating, advisory-only authority boundaries, redaction-first context manifests, safe read-only GitHub Connector usage pinned to immutable commits, a structured request/response schema, and durable, hash-bound advisory artifacts that can never masquerade as formal review evidence. |

## Installing a skill

For Codex CLI / ChatGPT Desktop, copy the skill folder into your Codex home:

```sh
cp -R skills/consult-chatgpt-pro ~/.codex/skills/
```

For Claude Code and other harnesses that discover `SKILL.md`-style skills, place the folder in
your harness's skill directory (e.g. `.claude/skills/`) and adapt the frontmatter if required.

## Design notes

The skills here favor **fail-closed contracts over prose guidance**: machine-checkable headers,
allowlist-first redaction gates, immutable commit pinning, computed (never self-asserted) validity
results, and explicit authority boundaries between advisory model output and repository-owned
review gates. They were extracted from a production multi-agent refactoring workflow in which
several frontier-model agents (planning, implementation, and adversarial review roles) collaborate
on one codebase under an independent review gate.

## Contributing

PRs adding skills are welcome if they follow the same shape: one folder per skill, a `SKILL.md`
with `name`/`description` frontmatter and imperative instructions, references split into separate
files, no secrets or personal data, and no repository-specific hard dependencies (integration
points may be described as examples).

## License

MIT
