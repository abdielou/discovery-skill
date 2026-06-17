# Agent Orchestration

Patterns for spawning background agents during discovery sessions. The cardinal rule: **never block the conversation for agent work**.

## Background Agents in Solo Mode

In **solo** presence (step-zero discovery), background research and expert agents stay fully available — they're a natural fit for the "try hard to *answer*" principle. But their output lands in the buckets under the **solo-mode write-permissions** (see `notes-format.md`): they may populate Fundamentals, Constraints, Contradictions, Parking Lot, and codebase-embodied Decisions, and may append Open Questions and Assumptions — they may **not** resolve Open Questions or elevate Assumptions into Decisions. A finding that requires human judgment becomes an Open Question, not an answer. The patterns below apply in both presences; in solo mode, the conversation you "don't block" is your own decomposition pass.

## Investigation Pattern

**Trigger**: a question requires reading 3+ files, involves cross-cutting concerns, or requires comparing implementation against spec.

1. Announce: "Let me investigate that — spawning a research agent"
2. Spawn a background agent with: the question, relevant file paths, expected output format (finding + evidence + recommendation)
3. Continue the conversation — don't block
4. When results arrive at a natural pause, summarize concisely

## Pre-fetch Pattern

**Trigger**: the explorer mentions a topic that will require codebase context.

1. Speculatively spawn a research agent to gather relevant context
2. Don't announce unless the explorer would benefit from knowing
3. Have the context ready before the discussion deepens

## Validation Pattern

**Trigger**: 5+ decisions have accumulated since last validation.

1. Spawn a validation agent to check internal consistency across all decisions
2. The agent should flag: contradictions, circular dependencies, decisions that invalidate assumptions
3. Report findings in the Contradictions section of the notes

## Expert Review Pattern

**Trigger**: a batch of changes to a spec, schema, or design is ready for audit.

Before spawning:
1. **Check the Expert Agents section** in the notes file — is there already an expert in this domain?
2. If yes and their status is `completed`: **resume** the agent (pass the agent ID) with the new scope. They already have prior context.
3. If yes and their status is `active`: wait for completion or queue the review.
4. If no: spawn a new expert and register them in the notes immediately.

Spawning a new expert:
1. Announce: "I'll have a [domain] expert audit this"
2. Spawn with specific review criteria and scope
3. **Record in notes immediately** — add an E# entry with agent ID, scope, and status `active`
4. When results return: update status to `completed`, record findings and decisions covered

Reuse guidance:
- A Schema Expert who reviewed D1-D5 can review D12-D15 too — resume, don't respawn
- Record `Can reuse for:` so your future self (or a continuation session) knows what each expert covers
- When the expert roster grows past 3-4 agents, note in the checkpoint which are highest value

## Batch Sync Pattern

**Trigger**: natural pause or topic transition, with decisions that affect downstream artifacts.

Don't sync artifacts after every decision. Instead:
1. Let decisions accumulate during natural conversation flow
2. At natural pauses or topic transitions, sync downstream artifacts in background
3. Announce: "Syncing [artifact] with decisions D14-D17 in the background"
4. If a sync reveals conflicts with existing artifacts, flag under Contradictions

## Interruption Queuing

Background agents return results unpredictably. Rules:
- **Queue** results that arrive during dense discussion
- **Surface** at natural pauses: topic transitions, when the explorer asks a new question, or when they seem to be wrapping up a thread
- **Interrupt immediately** only if a result directly contradicts something being decided right now
- When surfacing queued results, group them: "Three things came back while we were discussing X..."

## Context Window Management

Discovery sessions are long. Manage context proactively:
1. At ~60% context usage, write a full state snapshot to the notes file
2. Tell the explorer: "We're approaching context limits. Everything is checkpointed in the notes file. We can continue in a new session with `/discovery {topic}` and pick up exactly where we left off."
3. The notes file IS the continuity mechanism — keep it current at all times, not just at checkpoints
