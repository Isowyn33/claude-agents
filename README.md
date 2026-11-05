# dev-agents

Claude Code plugin with three stack-agnostic subagents:

| Agent | Role |
|---|---|
| `architect` | DDD / architecture expert. Designs the solution, writes the domain layer (model, ports, invariant tests) and an implementation plan. |
| `coder` | Pragmatic implementer. Turns a ticket, idea or plan into simple, tested code that follows the repo's conventions. |
| `reviewer` | Read-only reviewer. Checks business fit against the ticket, hunts bugs and edge cases, runs tests. |

Typical flow: **architect → coder → reviewer**.

Stack-specific rules belong in each project's `CLAUDE.md`; the agents read it.

## Install

```
/plugin marketplace add <github-user>/claude-agents
/plugin install dev-agents@ymurat-agents
```

For a private repo, git on that machine must be authenticated to GitHub (SSH key or credential helper).

## Update

Bump `version` in `.claude-plugin/plugin.json`, push, then run `/plugin marketplace update ymurat-agents`.
