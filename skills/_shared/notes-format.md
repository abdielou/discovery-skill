# Notes File Format

## Design Principles

- **Bullet lists, not tables** — trivially appendable, no row formatting overhead
- **Topic tags** on every item — enables fast scanning (`grep "\[auth\]" notes.md`)
- **Sections created on-demand** — start with Design Intent + Checkpoint only, add sections when items exist
- **Flat within sections** — no sub-grouping; tags carry the topic signal
- **Cold-start header** — Design Intent + latest Checkpoint always at top

## Template

```markdown
# {Topic} — Discovery Notes

## Status: In progress | Paused at {topic} | Complete
## Session: {date}

---

## Design Intent

(Write after first ~10 minutes. 2-3 sentences capturing the explorer's north-star goal.)

## Checkpoint (latest)

(Narrative synthesis of where we are. Updated every ~10 decisions or at topic transitions. Previous checkpoints move to archive at bottom.)

---

## Scope

**In:** (what we're designing/deciding)
**Out:** (what we've explicitly excluded and why)

---

## Fundamentals

(Irreducible truths discovered through first-principles decomposition. Decisions must trace back to these.)

- **F1** [topic-tag] Fundamental truth
  Derived from: what was decomposed to reach this
  Decisions anchored: D#, D#

---

(Sections below are created on-demand. Do not scaffold empty sections.)

## Decisions

- **D1** [topic-tag] Decision statement
  Why: rationale
  Rejected: alternatives considered
  Confidence: firm | tentative | provisional (→ Q#)

- **D2** [topic-tag] Decision statement
  Why: rationale
  Confidence: firm
  Supersedes: D1

## Assumptions

- **A1** [topic-tag] Assumption statement
  Status: unverified | validated | invalidated (→ D#)
  Impact if wrong: which decisions break

## Constraints

- **C1** [topic-tag] Constraint statement
  Source: who/what imposed this

## Open Questions

- **Q1** [topic-tag] Question
  Context: why this matters
  ~~Resolved: → D#~~  (strike through when answered)

## Parking Lot

- **P1** [topic-tag] Deferred topic
  Context: why it came up
  Blocks: D# or nothing

## Contradictions

- **X1** Conflict description
  Between: D# and D#
  Resolution: explorer's call | pending

## Expert Agents

(Registry of spawned expert agents. Consult before spawning new ones — reuse where possible.)

- **E1** [Domain] Expert — agent ID: {id}
  Scope: what they were asked to review
  Status: active | completed
  Findings: pass | issues: ...
  Decisions covered: D#-D#
  Can reuse for: topics this expert is qualified to review next

## Context & References

(Key files, codebase findings, architecture notes — anything the next agent needs)

---

## Checkpoint Archive

### CP1 — {topic} — {date}
(previous checkpoint narratives, moved here when superseded)
```

## The Seven Buckets

### Fundamentals (F1, F2, ...)
- Irreducible truths discovered through first-principles decomposition — not given externally (those are Constraints) but arrived at by asking "why?" until you can't go further
- Established during the decomposition phase early in the session, but can be added anytime
- Every decision should trace back to at least one fundamental. If a decision can't, it's worth questioning
- Unlike constraints (immutable inputs), fundamentals are discovered truths — they can be revised if the decomposition was wrong, but this should be rare and significant
- Record `Derived from:` to show the decomposition chain that produced this truth
- Record `Decisions anchored:` and update as new decisions reference this fundamental

### Decisions (D1, D2, ...)
- Source of truth — if a decision contradicts an earlier artifact, the decision wins
- Always include: rationale, alternatives rejected, confidence level
- Tag with topic labels: `[pricing]`, `[auth]`, `[data-model]` — items can have multiple tags
- When a decision supersedes an earlier one, add `Supersedes: D#` and mark the earlier one
- Confidence levels:
  - **firm** — committed, would need strong reason to revisit
  - **tentative** — best current thinking, revisit if we learn more
  - **provisional** — placeholder until a question is answered (`→ Q#`)

### Assumptions (A1, A2, ...)
- Things the explorer treats as true but hasn't verified
- Actively hunt for these — they're the most common source of downstream failure
- Track status: `unverified` → `validated` / `invalidated (→ D#)`
- When invalidated, trace which decisions are affected
- Record impact: "if wrong, D3 and D7 need redesign"

### Constraints (C1, C2, ...)
- Non-negotiable boundaries that shape the solution space
- Record the source (who/what imposed this constraint)
- These don't change through discussion — they're inputs, not outputs

### Open Questions (Q1, Q2, ...)
- Questions **you raised** that affect design or implementation
- Never have more than 1-2 open at a time — resolve before opening more
- When answered, strike through and create a Decision: `~~Q3~~ → D12`

### Parking Lot (P1, P2, ...)
- Topics **explicitly deferred by the explorer**
- You may flag when a parked item is relevant: "P3 may connect here — your call"
- But never advocate for pulling it in — that's the explorer's decision
- Record what the item blocks (if anything) so the explorer can prioritize

### Contradictions (X1, X2, ...)
- When you detect that a new statement or decision conflicts with an earlier one, flag it
- Present both sides neutrally: "D3 says X, but what you just said implies Y"
- The explorer resolves — you record the resolution

## Compacted Format

When notes grow large, they can be compacted via `/compact-notes {topic}` into multiple files for better retrieval and reduced context. Compaction is **lossless** — every item is preserved.

### Compacted directory structure

```
.planning/discovery/{topic}/
├── {topic}-notes.md          ← Index file (Design Intent, Checkpoint, Scope, file map, ID quick reference)
├── fundamentals.md           ← All F# entries (cross-cutting anchors)
├── topics/
│   ├── {tag1}.md             ← All items tagged [tag1], organized by bucket
│   ├── {tag2}.md             ← All items tagged [tag2], organized by bucket
│   └── general.md            ← Items with no topic tag
├── experts.md                ← Expert Agents registry
├── references.md             ← Context & References
└── checkpoint-archive.md     ← Archived checkpoints
```

### How to detect compacted notes

The index file (`{topic}-notes.md`) contains `## Compacted: yes` when notes have been compacted.

### Reading compacted notes

When continuing a session with compacted notes:
1. Read `{topic}-notes.md` (index) for Design Intent, Checkpoint, Scope, and file map
2. Read `fundamentals.md` for the foundational truths
3. Read topic files on-demand based on what the explorer wants to discuss
4. Don't read all topic files at once — the point of compaction is to reduce context

### Writing to compacted notes

When adding new items during a session with compacted notes:
- Write new items to the appropriate topic file under `topics/`
- New fundamentals go to `fundamentals.md`
- Update the index file's ID Quick Reference and item counts
- If a new topic tag appears, create a new `topics/{tag}.md` file

### Cross-referenced items

Items with multiple topic tags live in their primary tag's file (first tag listed). Other tag files contain a cross-reference pointer:

```markdown
- ↗ **D5** also tagged [{this-tag}] — see `topics/{primary-tag}.md`
```

## Why This Structure

1. **Append-only workflow**: adding an item = add a bullet. No table columns to maintain.
2. **Cross-bucket scanning**: `grep "\[auth\]" notes.md` finds all auth-related items in one pass.
3. **Contradiction detection**: all decisions in one flat list — scan D1-D30 linearly.
4. **No classification tax**: no "which thread does this belong in?" decision. Tags are lightweight and an item can have multiple: `[auth][ux]`.
5. **Progressive growth**: a 5-minute session has 3 sections. A 2-hour session has 10. No empty scaffolding.
6. **Cold start in 10 seconds**: Design Intent → latest Checkpoint → section headers → ready.
