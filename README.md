# Discovery Skill

A structured facilitation skill that guides discovery sessions with first-principles decomposition, persistent notes, and background expert agents.

Compatible with **Claude Code** and **OpenCode**.

## What it does

The discovery skill turns the AI into a **Facilitator** for structured thinking sessions. You (the Explorer) own the substance — domain knowledge, decisions, priorities. The AI owns the process — pacing, transitions, completeness checks, synthesis.

### Key features

- **First-principles decomposition** — breaks down problems to irreducible truths before exploring solutions
- **Seven-bucket notes system** — Fundamentals, Decisions, Assumptions, Constraints, Open Questions, Parking Lot, Contradictions
- **Background expert agents** — spawns domain experts (Schema, API, Security, UX, etc.) for async review without blocking conversation
- **Session continuity** — persistent notes file enables pause/resume across sessions
- **Assumption hunting** — actively surfaces unstated assumptions and tracks their impact
- **Contradiction detection** — flags conflicts between decisions in real time
- **Socrates integration** — dialectic stress-testing for deep or contentious decompositions (optional, via [zetaminusone/socrates](https://github.com/ZetaMinusOne/socrates))

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
/discovery                        # asks what you'd like to explore
```

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

## License

MIT
