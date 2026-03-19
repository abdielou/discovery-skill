---
name: compact-notes
description: Compact discovery notes into multiple well-organized files for better retrieval and reduced context. Use when notes grow large, or when the user says "compact notes", "organize notes", "split notes", or invokes /compact-notes.
---

# Compact Discovery Notes

Compact a discovery session's notes file into multiple well-organized files. Compaction is **lossless** — every fact, rule, decision, assumption, fundamental, constraint, question, parking lot item, contradiction, expert entry, and reference must be preserved exactly. Nothing is summarized away or dropped.

## Argument

$ARGUMENTS

Use the argument as the topic name. If no argument is provided, look for the most recent notes file under `.planning/discovery/` or ask the user which topic to compact.

## Setup

Read the notes format reference before starting:
- `../_shared/notes-format.md` — notes file structure, bucket definitions, compacted format spec

## Why Compact

A single notes file works well for short sessions, but as sessions grow (30+ decisions, multiple topics), the file becomes:
- **Too large** for efficient cold-start — agents read more context than needed
- **Topic-interleaved** — items about `[auth]` are scattered between `[pricing]` and `[data-model]`
- **Hard to target** — retrieving items for one topic requires reading the entire file

Compaction solves this by splitting notes into **topic-focused files** while maintaining a lightweight **index** for cold-start orientation.

## Compaction Rules

### Lossless guarantee

1. **Every item** (F#, D#, A#, C#, Q#, P#, X#, E#) must appear in exactly one output file
2. **All metadata** (rationale, confidence, status, cross-references, tags, derived-from chains) must be preserved verbatim
3. **Design Intent** and **latest Checkpoint** are preserved in the index file
4. **Checkpoint Archive** entries are all preserved
5. **Scope** (In/Out) is preserved in the index file
6. **Context & References** are preserved
7. After compaction, `grep` for any item ID (e.g., `D14`) across all output files must return the same content as in the original

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
├── references.md             ← Context & References section (if non-empty)
└── checkpoint-archive.md     ← All archived checkpoints (if any exist)
```

## Process

### Step 1 — Read and parse

1. Read the existing notes file at `.planning/discovery/{topic}/{topic}-notes.md`
2. If the file doesn't exist, tell the user and stop
3. If the file is already compacted (has `## Compacted: yes` in the index), tell the user notes are already compacted and ask if they want to re-compact (useful if new items were appended since last compaction)
4. Parse all sections and items, preserving every line

### Step 2 — Extract topic tags

1. Scan all items for topic tags (`[tag]` patterns)
2. Build a map: `tag → list of items`
3. Identify items with no tags → assign to `general`
4. Identify items with multiple tags → primary tag is the first listed, cross-reference the rest

### Step 3 — Write output files

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

### Step 4 — Verify

After writing all files:
1. Count total items in output files and compare to original — must match
2. Spot-check: pick 3 random item IDs and verify they appear in the correct output file with full metadata
3. Report to the user: "Compacted {n} items across {m} topic files. All items preserved."

### Step 5 — Summary

Tell the user:
- How many items were compacted
- How many topic files were created
- The file map (which topics have which items)
- Remind them that `/discovery {topic}` will automatically read the compacted structure on next session
