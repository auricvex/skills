# Agent Skills — Red-Team Audits

Portable AI agent skills following the [Agent Skills open standard](https://agentskills.io): each skill is a folder with a `SKILL.md` that teaches an agent a repeatable workflow. Compatible with any agent that implements the standard (Claude Code, Cursor, GitHub Copilot, VS Code, OpenAI Codex, and others).

## Skills

| Skill | What it does |
|---|---|
| `redteam-spec` | Red-team audit a technical specification (protocol, architecture, design spec): finds contradictions, gaps, and ambiguities via multi-agent fan-out, double-verified findings, and hard stopping rules against scope creep. |
| `redteam-impl` | Red-team audit a software implementation against its spec: conformance checking plus security vulnerabilities, bugs, inefficiency, and undefined behavior — same verification and boundary discipline. |

Both skills enforce the same discipline: multi-agent fan-out when available, every finding verified twice (false positives discarded), inputs gathered via question tools when missing, and hard stopping rules — scope box, no growth/redesign, convergence rule (a round with zero new critical/major findings = STABLE), fix budget, decision freeze, time-box.

## Install

### Via the skills CLI (easiest)

```bash
npx skills add auricvex/skills
# or with Bun
bunx skills add auricvex/skills
```

Useful flags: `--skill redteam-spec` (install one skill), `-g` (global install), `-a claude-code` (target a specific agent), `-y` (skip prompts), `--list` (preview without installing). After installing, restart your agent so it picks up the new skills.

### Manual

Copy a skill folder into your agent's skills directory:

- Claude Code: `~/.claude/skills/` (personal) or `.claude/skills/` (project)
- GitHub Copilot (VS Code): `.github/skills/`, `.claude/skills/`, or `.agents/skills/`
- OpenAI Codex: `.agents/skills/` or `~/.agents/skills/`

The agent discovers skills by the `description` in each `SKILL.md` frontmatter and loads the full instructions when a task matches — no configuration needed.

## Usage

Just ask your agent in plain language. Examples:

- "Audit this protocol spec for contradictions and gaps" → triggers `redteam-spec`
- "Red-team my implementation against the spec, check for security issues too" → triggers `redteam-impl`

If a skill needs inputs you didn't provide (spec documents, implementation directory, docs directory), it will ask you for them — one question at a time.

## Layout

Each skill is self-contained:

```
redteam-spec/
└── SKILL.md    # frontmatter (name, description) + Purpose / Workflow / Output Contract / Operating Rules
redteam-impl/
└── SKILL.md    # same structure
```

No scripts, no dependencies, no build step.

## License

See [LICENSE](LICENSE).
