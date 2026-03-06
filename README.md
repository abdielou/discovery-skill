# Discovery Skill for Claude Code

A structured facilitation skill that guides discovery sessions with first-principles decomposition, persistent notes, and background expert agents.

## What it does

The `/discovery` command turns Claude into a **Facilitator** for structured thinking sessions. You (the Explorer) own the substance — domain knowledge, decisions, priorities. Claude owns the process — pacing, transitions, completeness checks, synthesis.

### Key features

- **First-principles decomposition** — breaks down problems to irreducible truths before exploring solutions
- **Seven-bucket notes system** — Fundamentals, Decisions, Assumptions, Constraints, Open Questions, Parking Lot, Contradictions
- **Background expert agents** — spawns domain experts (Schema, API, Security, UX, etc.) for async review without blocking conversation
- **Session continuity** — persistent notes file enables pause/resume across sessions
- **Assumption hunting** — actively surfaces unstated assumptions and tracks their impact
- **Contradiction detection** — flags conflicts between decisions in real time
- **Socrates integration** — dialectic stress-testing for deep or contentious decompositions (optional, via [zetaminusone/socrates](https://github.com/ZetaMinusOne/socrates))

## Installation

### Plugin Marketplace (recommended)

**Interactive:**

```
/plugin
```

Go to the Marketplaces tab, add `abdielou/discovery-skill`, then switch to the Discover tab and install.

**CLI:**

```
/plugin marketplace add abdielou/discovery-skill
/plugin install discovery@abdielou-discovery-skill
```

### Manual (git clone)

**User-level** (available in all projects):

```bash
git clone https://github.com/abdielou/discovery-skill.git ~/.claude/skills/discovery-skill
```

**Project-level** (available in one project):

```bash
git clone https://github.com/abdielou/discovery-skill.git .claude/skills/discovery-skill
```

### Local development

```bash
claude --plugin-dir ./path/to/discovery-skill
```

## Usage

```
/discovery <topic or context>
```

**Examples:**

```
/discovery API redesign for billing module
/discovery new onboarding flow — here's the wireframe [attach]
/discovery                        # asks what you'd like to explore
```

**Continuing a previous session:**

```
/discovery billing-api            # reads existing notes and picks up where you left off
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

```
discovery-skill/
├── .claude-plugin/
│   ├── plugin.json            # Plugin metadata and command registration
│   └── marketplace.json       # Marketplace distribution config
├── skills/
│   ├── discovery/
│   │   └── discovery.md       # Main skill definition
│   └── _shared/
│       ├── notes-format.md    # Notes file structure and bucket definitions
│       ├── facilitation-playbook.md  # Facilitation techniques
│       └── agent-orchestration.md    # Background agent patterns
├── .gitignore
└── README.md
```

## License

MIT
