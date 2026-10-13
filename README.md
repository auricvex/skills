# skills

Portable AI agent skills following the [Agent Skills open standard](https://agentskills.io) (originally Anthropic, now industry-wide): each skill is a folder with a `SKILL.md` that teaches an agent a repeatable workflow. Compatible with any agent that implements the standard (Claude Code, Cursor, GitHub Copilot, VS Code, OpenAI Codex, and others).

## Skills

| Skill | What it does |
|---|---|
| `redteam-spec` | Red-team audit a technical specification (protocol, architecture, design): contradictions, gaps, ambiguities — multi-agent fan-out, double-verified findings, hard stopping rules against scope creep. |
| `redteam-impl` | Red-team audit an implementation against its spec: conformance checking plus security vulnerabilities, bugs, inefficiency, undefined behavior — same verification and boundary discipline. |

## Install

Copy a skill folder into your agent's skills directory — e.g. `~/.claude/skills/` (personal) or `.claude/skills/` (project). The agent discovers skills by the `description` in each `SKILL.md` frontmatter and loads the full instructions when a task matches.

## Layout

Each skill is self-contained: `SKILL.md` with frontmatter (`name`, `description`), then Purpose / Workflow / Output Contract / Operating Rules. No scripts, no dependencies.
