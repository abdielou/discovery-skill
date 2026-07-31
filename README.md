# Discovery Skill

A structured facilitation skill that guides discovery sessions with first-principles decomposition, persistent notes, and background expert agents.

Compatible with **Claude Code** and **OpenCode**.

## What it does

The discovery skill turns the AI into a **Facilitator** for structured thinking sessions. You (the Explorer) own the substance — domain knowledge, decisions, priorities. The AI owns the process — pacing, transitions, completeness checks, synthesis.

### Key features

- **Two-axis modes** — *presence* (solo step-zero vs. facilitated) and *depth* (deep first-principles vs. shallow mapping) are set independently (see [Modes](#modes-presence--depth))
- **First-principles decomposition** — breaks down problems to irreducible truths before exploring solutions
- **Seven-bucket notes system** — Fundamentals, Decisions, Assumptions, Constraints, Open Questions, Parking Lot, Contradictions
- **Background expert agents** — spawns domain experts (Schema, API, Security, UX, etc.) for async review without blocking conversation
- **Session continuity** — persistent notes file enables pause/resume across sessions
- **Assumption hunting** — actively surfaces unstated assumptions and tracks their impact
- **Contradiction detection** — flags conflicts between decisions in real time
- **Simplified Technical English**: every message and every note entry follows ASD-STE100 style, adapted to the session language when the session is not in English
- **Socrates integration** — dialectic stress-testing for deep or contentious decompositions (optional, via [zetaminusone/socrates](https://github.com/ZetaMinusOne/socrates))

### Modes (presence × depth)

Discovery runs along two **independent** axes — all four combinations are valid:

- **Presence — who is driving:**
  - **solo** — the agent runs discovery alone against a ticket and the codebase as its only sources of truth. This is *step zero* of a development workflow: it populates the notes, then stops at an **executive summary** and waits for the human. It never resolves Open Questions or unconfirmed Assumptions on its own.
  - **facilitated** *(default)* — the agent runs discovery with you, turn by turn. This is where Open Questions and Assumptions get resolved.
- **Depth — how much rigor:**
  - **deep** *(default)* — full first-principles recursive decomposition to irreducible truths.
  - **shallow** — light mapping for well-understood domains or changes that follow an existing pattern; the recursive "why?" is skipped. Depth scales the decomposition, not the buckets — even a shallow pass returns named Assumptions and Open Questions.

A discovery typically **starts solo and transitions to facilitated** when you join: the agent hands you a pre-read (problem statement, proposed approach, a recommended depth, and a ranked list of decisions it needs from you — each with a recommended default), and you converge from the already-populated notes file. The agent proposes depth after its first read; you confirm it.

## Installation

### Claude Code

**Plugin Marketplace (recommended):**

```
/plugin marketplace add abdielou/discovery-skill
/plugin install discovery@abdielou-discovery-skill
```

**Manual:**

```bash
git clone https://github.com/abdielou/discovery-skill.git ~/.claude/skills/discovery-skill
```

### OpenCode

Copy or clone this repository into your OpenCode skills directory:

```bash
# macOS/Linux
git clone https://github.com/abdielou/discovery-skill.git ~/.agents/skills/discovery-skill

# Windows
git clone https://github.com/abdielou/discovery-skill.git %USERPROFILE%\.agents\skills\discovery-skill
```

Or place it in the project-level skills directory if your OpenCode setup supports it.

### Local development

```bash
claude --plugin-dir ./path/to/discovery-skill
```

## Usage

### Claude Code

```
/discovery <topic or context>
```

**Examples:**

```
/discovery API redesign for billing module
/discovery new onboarding flow — here's the wireframe [attach]
/discovery --solo billing-api     # step-zero solo pass — produces a pre-read, then waits for you
/discovery                        # asks what you'd like to explore
```

The `solo` keyword (or `--solo` flag) starts a step-zero pass; without it, discovery is facilitated. A solo-populated notes file resumes into a facilitated session like any other — run `/discovery <topic>` again to join.

**Continuing a previous session:**

```
/discovery billing-api            # reads existing notes and picks up where you left off
```

### OpenCode

The skill activates automatically when you use phrases like:
- "discovery session"
- "let's explore"
- "facilitated discussion"
- "help me think through"
- "solo discovery" / "do a step-zero pass" (for the solo presence mode)
- "compact notes" (for organizing existing discovery notes)

Or explicitly load it:

```
Load the discovery skill
```

**Examples:**

```
Let's do a discovery session on the API redesign for the billing module.
```

```
Help me think through the new onboarding flow. Here's the wireframe...
```

```
Compact the notes for billing-api discovery
```

## How it works

1. **Design intent** — establishes the north-star goal
2. **Decomposition** — recursive "why?" until irreducible truths (Fundamentals) emerge
3. **Facilitated exploration** — diverge-then-converge questioning with real-time note-taking
4. **Expert reviews** — background agents audit decisions without blocking
5. **Checkpoint synthesis** — periodic narrative summaries catch drift and misunderstandings
6. **Wrap-up** — surfaces unresolved items and produces a complete handoff artifact

All decisions, assumptions, and context are captured in `.planning/discovery/{topic}/{topic}-notes.md` — the single source of truth for the session.

## Repository structure

### OpenCode layout

```
discovery-skill/
├── SKILL.md                          # Main skill definition
├── references/
│   ├── notes-format.md              # Notes file structure and bucket definitions
│   ├── facilitation-playbook.md     # Facilitation techniques
│   ├── agent-orchestration.md       # Background agent patterns
│   └── compact-notes.md             # Note compaction instructions
├── .claude-plugin/                   # Claude Code plugin metadata
│   ├── plugin.json
│   └── marketplace.json
├── skills/                           # Claude Code skill files
│   ├── discovery/
│   │   ├── discovery.md
│   │   └── compact-notes.md
│   └── _shared/
│       ├── notes-format.md
│       ├── facilitation-playbook.md
│       └── agent-orchestration.md
├── .gitignore
└── README.md
```

## Development

This skill is maintained in **two parallel layouts** — one for Claude Code, one for OpenCode — that share the same content. Two conventions keep them consistent:

**1. Mirror every content edit across both layouts.** Each file has a counterpart that must be kept in sync:

| Claude Code | OpenCode |
|---|---|
| `skills/discovery/discovery.md` | `SKILL.md` |
| `skills/_shared/notes-format.md` | `references/notes-format.md` |
| `skills/_shared/facilitation-playbook.md` | `references/facilitation-playbook.md` |
| `skills/_shared/agent-orchestration.md` | `references/agent-orchestration.md` |
| `skills/discovery/compact-notes.md` | `references/compact-notes.md` |

The two copies differ only in expected ways — frontmatter, internal paths (`../_shared/` vs `references/`), the `Argument`/`Topic` heading, and the OpenCode compaction section. Everything else should match. To check before committing:

```bash
diff <(sed 's|\.\./_shared/|references/|g; s/main discovery skill/main SKILL.md/g' skills/discovery/discovery.md) SKILL.md
```

**2. Bump the version in both manifests.** The version lives in two files that must stay in sync:

- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

## License

MIT
