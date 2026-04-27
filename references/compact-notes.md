# Compact Discovery Notes

Compact a discovery session's notes file into multiple well-organized files. Compaction is **lossless** — every fact, rule, decision, assumption, fundamental, constraint, question, parking lot item, contradiction, expert entry, and reference must be preserved exactly. Nothing is summarized away or dropped.

## Argument

$ARGUMENTS

Use the argument as the topic name. If no argument is provided, look for the most recent notes file under `.planning/discovery/` or ask the user which topic to compact.

## Setup

Read the notes format reference before starting:
- `references/notes-format.md` — notes file structure, bucket definitions, compacted format spec

## Why Compact

A single notes file works well for short sessions, but as sessions grow (30+ decisions, multiple topics), the file becomes:
- **Too large** for efficient cold-start — agents read more context than needed
- **Topic-interleaved** — items about `[auth]` are scattered between `[pricing]` and `[data-model]`
- **Hard to target** — retrieving items for one topic requires reading the entire file

Compaction solves this by splitting notes into **topic-focused files** while maintaining a lightweight **index** for cold-start orientation.

## Compaction Rules

### Lossless guarantee

No facts or rules may be lost. Compaction may consolidate (merge duplicates, compact resolved items) but never discard information.

1. **Every item** (F#, D#, A#, C#, Q#, P#, X#, E#) must either appear in an output file or be accounted for in the Consolidation Log with a clear merge target
2. **All active metadata** (rationale, confidence, status, cross-references, tags, derived-from chains) must be preserved — only metadata made redundant by consolidation (e.g., "impact if wrong" on a validated assumption) may be dropped
3. **Superseded decisions** are preserved in a `### Superseded` subsection — not deleted, just separated from active decisions
4. **Design Intent** and **latest Checkpoint** are preserved in the index file
5. **Checkpoint Archive** entries are all preserved
6. **Scope** (In/Out) is preserved in the index file
7. **Context & References** are preserved
8. After compaction, `grep` for any item ID (e.g., `D14`) across all output files or the Consolidation Log must return a result

### Organization strategy

Items are organized by **topic tag** — the `[tag]` labels already on every item.

- Each unique topic tag gets its own file
- Items with **multiple tags** (e.g., `[auth][ux]`) go into the file for their **primary tag** (the first one listed), with a cross-reference note added to the other tag files
- Items with **no tag** go into a `general.md` file
- **Fundamentals** go into their own `fundamentals.md` file since they are cross-cutting anchors that decisions across all topics reference
- **Expert Agents** go into their own `experts.md` file since they are a registry, not topic-scoped

### Output structure

```
.planning/discovery/{topic}/
├── {topic}-notes.md          ← Rewritten as index (Design Intent, Checkpoint, Scope, file map)
├── fundamentals.md           ← All F# entries with full metadata
├── topics/
│   ├── {tag1}.md             ← All D#, A#, C#, Q#, P#, X# items tagged [tag1]
│   ├── {tag2}.md             ← All items tagged [tag2]
│   └── ...
├── experts.md                ← Expert Agents registry (if any E# entries exist)
├── references.md             ← Context & References (if non-empty)
└── checkpoint-archive.md     ← All archived checkpoints (if any exist)
```

## Process

### Step 1 — Read and parse

1. Read the existing notes file at `.planning/discovery/{topic}/{topic}-notes.md`
2. If the file doesn't exist, tell the user and stop
3. If the file is already compacted (has `## Compacted: yes` in the index), tell the user notes are already compacted and ask if they want to re-compact (useful if new items were appended since last compaction)
4. Parse all sections and items, preserving every line

### Step 2 — Consolidation analysis

Before splitting into files, reason through all items to consolidate and de-duplicate. This is the intelligence step — not just structural reorganization but semantic compression.

#### 2a — Identify duplicates and near-duplicates

Scan all items across all buckets for:
- **Exact duplicates** — same fact recorded under different IDs (e.g., D3 and D15 say the same thing). Keep the one with richer metadata; drop the other and record a mapping (e.g., "D15 merged into D3").
- **Near-duplicates** — two items that express the same underlying fact with different wording (e.g., A2: "Users won't exceed 10k" and C4: "System must handle up to 10k users"). Merge into a single item, preserving the richer metadata and the stricter bucket classification (a Constraint outranks an Assumption if both say the same thing). Record the merge.
- **Superseded chains** — decisions that have been superseded (D2 `Supersedes: D1`). Keep the final decision in the active section; move the superseded one into a `### Superseded` subsection in the same topic file so history is preserved but clearly separated from current state.

#### 2b — Consolidate resolved items

- **Resolved questions** — questions that are struck through (`~~Q3~~ → D12`) can be collapsed: keep only the cross-reference line (`~~Q3~~ Resolved → D12`) rather than preserving the full question context, since the answer lives in the linked decision.
- **Validated assumptions** — assumptions marked `validated` can be converted to a compact one-liner retaining the original ID, statement, and validation note, dropping the "impact if wrong" since it's no longer relevant.
- **Invalidated assumptions** — keep full detail since the linked decision (e.g., `invalidated → D8`) needs the context of what was wrong and why.

#### 2c — Tighten cross-references

- Walk all `Decisions anchored:` lists in Fundamentals and verify they're up to date — add any decisions that reference a fundamental but aren't listed, remove references to superseded decisions.
- Walk all `Impact if wrong:` entries in Assumptions and verify the listed decisions still exist and haven't been superseded.
- Walk all `Supersedes:` chains in Decisions and verify they're consistent — no circular supersession, no references to nonexistent IDs.

#### 2d — Record consolidation log

Create a consolidation summary to include in the index file:

```markdown
## Consolidation Log

- D15 merged into D3 (duplicate: both specified REST for public API)
- A2 merged into C4 (assumption promoted to constraint: 10k user cap)
- D1 moved to superseded (superseded by D2)
- ~~Q3~~ collapsed (resolved → D12)
- A5 compacted (validated)
- F2 `Decisions anchored` updated: added D14, removed D1 (superseded)
```

This log ensures the compaction is auditable — anyone can trace what changed.

### Step 3 — Extract topic tags

1. Scan all remaining items (post-consolidation) for topic tags (`[tag]` patterns)
2. Build a map: `tag → list of items`
3. Identify items with no tags → assign to `general`
4. Identify items with multiple tags → primary tag is the first listed, cross-reference the rest

### Step 4 — Write output files

Write each file with clear headers and complete content. Use the formats below.

#### Index file (`{topic}-notes.md`)

```markdown
# {Topic} — Discovery Notes

## Status: {preserved from original}
## Session: {preserved from original}
## Compacted: yes

---

## Design Intent

{preserved verbatim from original}

## Checkpoint (latest)

{preserved verbatim from original}

---

## Scope

{preserved verbatim from original}

---

## File Map

| File | Contents | Item count |
|------|----------|------------|
| `fundamentals.md` | Core truths (F1-F{n}) | {count} |
| `topics/{tag1}.md` | [{tag1}] decisions, assumptions, constraints, questions | {count} |
| `topics/{tag2}.md` | [{tag2}] decisions, assumptions, constraints, questions | {count} |
| ... | ... | ... |
| `experts.md` | Expert agent registry | {count} |
| `references.md` | Context & references | — |
| `checkpoint-archive.md` | Previous checkpoints | {count} |

## ID Quick Reference

(List every item ID and its location for fast lookup)

- F1-F{n} → `fundamentals.md`
- D1, D3, D7 → `topics/{tag1}.md`
- D2, D4-D6 → `topics/{tag2}.md`
- A1, A3 → `topics/{tag1}.md`
- ...

## Consolidation Log

(What was merged, collapsed, or reorganized during compaction — ensures auditability)

- D15 merged into D3 (duplicate: both specified REST for public API)
- A2 merged into C4 (assumption promoted to constraint: 10k user cap)
- D1 moved to superseded (superseded by D2)
- ~~Q3~~ collapsed (resolved → D12)
- A5 compacted (validated)
- F2 `Decisions anchored` updated: added D14, removed D1 (superseded)
```

#### Fundamentals file (`fundamentals.md`)

```markdown
# {Topic} — Fundamentals

(Irreducible truths discovered through first-principles decomposition.)

- **F1** [topic-tag] Fundamental truth
  Derived from: ...
  Decisions anchored: D#, D#

- **F2** ...
```

#### Topic file (`topics/{tag}.md`)

```markdown
# {Topic} — [{tag}]

Items related to [{tag}].

## Decisions

- **D1** [tag] Decision statement
  Why: rationale
  Rejected: alternatives considered
  Confidence: firm
  ...

### Superseded

(Decisions that have been replaced by newer ones — kept for history, not active.)

- **D1** [tag] Original decision statement (superseded by D2)
  Why: original rationale
  ...

## Assumptions

- **A1** [tag] Assumption statement
  Status: unverified
  Impact if wrong: ...

## Constraints

- **C1** [tag] Constraint statement
  Source: ...

## Open Questions

- **Q1** [tag] Question
  Context: ...

## Parking Lot

- **P1** [tag] Deferred topic
  Context: ...
  Blocks: ...

## Contradictions

- **X1** Conflict description
  Between: D# and D#
  Resolution: ...
```

Only include sections that have items — do not scaffold empty sections in topic files.

For **cross-referenced items** (items whose primary tag is different), add a reference line:

```markdown
## Decisions

- ↗ **D5** also tagged [{tag}] — see `topics/{primary-tag}.md`
```

#### Experts file (`experts.md`)

```markdown
# {Topic} — Expert Agents

- **E1** [Domain] Expert — agent ID: {id}
  Scope: ...
  Status: active | completed
  Findings: ...
  Decisions covered: D#-D#
  Can reuse for: ...
```

#### References file (`references.md`)

```markdown
# {Topic} — Context & References

{preserved verbatim from original}
```

#### Checkpoint Archive (`checkpoint-archive.md`)

```markdown
# {Topic} — Checkpoint Archive

### CP1 — {topic} — {date}
{preserved verbatim}

### CP2 — ...
```

### Step 5 — Verify

After writing all files:
1. Count total unique items in output files — every original item must be accounted for (either present in an output file, or listed in the Consolidation Log as merged/collapsed)
2. Verify no item ID is orphaned — every ID from the original either exists in an output file or has a merge entry in the Consolidation Log
3. Spot-check: pick 3 random item IDs and verify they appear in the correct output file with full metadata
4. Verify cross-reference integrity — every `↗` pointer in topic files points to an existing file and item

### Step 6 — Summary

Tell the user:
- How many items were compacted and how many remain after consolidation
- What was consolidated (summarize the Consolidation Log)
- How many topic files were created
- The file map (which topics have which items)
- Remind them that `/discovery {topic}` will automatically read the compacted structure on next session
