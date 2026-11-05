# dev-agents

A Claude Code plugin with three stack-agnostic subagents that work as a small dev team:

| Agent | Role | Edits code? |
|---|---|---|
| `architect` | DDD / architecture expert. Understands the problem, compares options, writes the **domain layer** (model, ports, invariant tests) and an implementation plan. | Domain layer only |
| `coder` | Pragmatic implementer. Turns a ticket, an idea or the architect's plan into simple, tested code that follows the repo's conventions. | Yes |
| `reviewer` | Skeptical reviewer. Checks the change against the requirements, hunts bugs and edge cases, runs the tests. | No (read-only) |

Typical flow: **architect → coder → reviewer**.

The agents detect the language and tooling from the repo. Put stack-specific rules in the project's `CLAUDE.md`; the agents read it.

---

## Install from scratch (~5 minutes)

Use this on a brand-new machine, for example right before a live coding session.

### 1. Install Claude Code

macOS / Linux / WSL:
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Windows (PowerShell):
```powershell
irm https://claude.ai/install.ps1 | iex
```

Alternative, if Node.js 18+ is installed:
```bash
npm install -g @anthropic-ai/claude-code
```

Then start it and log in. A browser window opens for a Claude account or API key:
```bash
claude
```

### 2. Give git access to this private repo

This repo is **private**, so git on the machine must be able to read it. On a machine that isn't yours, use a **short-lived, read-only token** and don't store it.

1. Create a token at https://github.com/settings/personal-access-tokens/new
   - Expiration: **7 days** (or the shortest option)
   - Repository access: *Only select repositories* → `claude-agents`
   - Permissions → Repository permissions → **Contents: Read-only**
2. Clone the repo without saving the credential:
   ```bash
   git -c credential.helper= clone https://github.com/Isowyn33/claude-agents.git
   ```
   - Username: `Isowyn33`
   - Password: **paste the token** (GitHub rejects your account password). Nothing appears while you paste; that's normal.

### 3. Install the plugin from the local clone

In Claude Code:
```
/plugin marketplace add ./claude-agents
/plugin install dev-agents@ymurat-agents
```
Use the path where you cloned the repo, relative to where you started `claude`, or an absolute path.

Then **restart Claude Code** (`/exit`, then `claude`).

> On your own machine, where git is already logged in to GitHub, you can skip the clone and install straight from GitHub:
> ```
> /plugin marketplace add Isowyn33/claude-agents
> /plugin install dev-agents@ymurat-agents
> ```

### 4. Check that it works

```
/agents
```
You should see `architect`, `coder` and `reviewer`, possibly prefixed with `dev-agents:`.

---

## Quick start for a live coding session

Go into the project folder and start Claude Code there:
```bash
cd path/to/exercise
claude
```

Give context first. If the repo has no `CLAUDE.md`, a one-liner in your first prompt is enough (language, test command, constraints).

**1. Design: understand and plan**
```
Use the architect agent. Here is the exercise: <paste the statement>.
Keep it simple, it's a 1h exercise. Model the domain, write it with
tests for the invariants, and give me a short step-by-step plan.
```

**2. Implement: one step at a time**
```
Use the coder agent to implement step 1 of the plan. Run the tests.
```
Small steps let you follow along and explain each one to the interviewer.

**3. Review: before you say "done"**
```
Use the reviewer agent on the current changes against the exercise statement.
Focus on edge cases and missing requirements.
```

**Tips**
- Ask the interviewer up front whether AI tools are allowed and how they want you to use them.
- Read and explain what the agents produce. You're the one being interviewed, not the agents.
- For tiny changes, skip the agents and just ask Claude directly. Agents shine on design, multi-file work and review.
- Ask the architect for options (*"give me two approaches and the trade-offs"*). Explaining a trade-off is often what interviewers want to hear.

---

## Clean up a borrowed machine

```
/plugin uninstall dev-agents@ymurat-agents
/plugin marketplace remove ymurat-agents
```
```bash
rm -rf claude-agents          # the clone
```
Then revoke the token at https://github.com/settings/personal-access-tokens, and log out of Claude Code (`/logout`) if the account was yours.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `Password authentication is not supported` | You entered your GitHub password. Paste a **token** instead (step 2). |
| `Repository not found` when cloning | The token doesn't include the `claude-agents` repo, or it lacks *Contents: Read*. |
| Agents missing from `/agents` | Restart Claude Code after installing. Check `/plugin` to see whether `dev-agents` is installed and enabled. |
| `/plugin marketplace add` fails on the path | Use an absolute path to the clone (e.g. `/home/me/claude-agents`). |
| No time for any of this | Last resort: copy `agents/*.md` into `~/.claude/agents/` (or `.claude/agents/` in the project) and restart Claude Code. They work the same, just without the plugin wrapper. |

---

## Maintaining

Edit the agents in `agents/`, then:
1. Bump `version` in `.claude-plugin/plugin.json`
2. Commit and push
3. On each machine: `/plugin marketplace update ymurat-agents`

Structure:
```
.claude-plugin/
  plugin.json        # plugin "dev-agents"
  marketplace.json   # marketplace "ymurat-agents"
agents/
  architect.md
  coder.md
  reviewer.md
```
