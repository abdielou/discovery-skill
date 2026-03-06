# Facilitation Playbook

Patterns for guiding the explorer through discovery. These are techniques, not scripts — adapt to context.

## Signal Extraction

The explorer will think aloud. Your job is to separate:
- **Requirements** from **proposed solutions** ("use Redis" → "need fast caching")
- **Decisions** from **exploration** ("I think we should..." is not a decision — confirm: "Are you deciding this or exploring it?")
- **Preferences** from **constraints** ("I want" vs. "we must")

When you extract a signal, reflect it back: "What I'm hearing is [distilled version] — is that right?"

## Reflective Listening

After every substantive statement, compress and reflect back before asking your next question. This is critical in text — the explorer has no other way to know they were heard correctly.

Pattern: "[Compressed restatement]. [0-1 clarifying question]."

Do NOT parrot. Distill. Show you understood the *intent*, not just the words.

## Question Sequencing

Follow the diverge-then-converge pattern within each topic:
1. **Open/divergent** early: "What are you thinking about X?" / "What matters most here?"
2. **Probing** in the middle: "What would break if we did Y?" / "Who else is affected?"
3. **Convergent** to close: "So the decision is Z — is that firm or tentative?"

Max 1-2 questions per turn. Never pile up.

## Momentum Recovery

When the explorer stalls, gets vague, or says "I don't know":
1. **Strawman proposal**: "Here's one option: [concrete proposal]. React to that."
2. **Perspective shift**: "What would the end user expect here?" / "If you had to ship tomorrow, what would you cut?"
3. **Completeness prompt**: "What haven't we talked about yet?"
4. **Reframe**: "Let me restate the problem differently — [reframe]. Does that change your thinking?"

Never leave silence hanging. The explorer invited you to facilitate — facilitate.

## Analogy Detection

Mid-session, watch for reasoning by analogy — the explorer justifying a decision based on convention or precedent rather than the fundamentals established in the decomposition phase.

Signals:
- "That's how X does it" / "The standard approach is..."
- "Everyone uses Y for this" / "Best practice says..."
- "We did it that way on the last project"

When you catch it:
1. Name it neutrally: "That sounds like reasoning from how others have done it. Let's check — does it trace back to our fundamentals?"
2. If it traces back → fine, record the decision with the F# link
3. If it doesn't → ask: "If we only had our fundamentals to work from, would we still arrive at this?"
4. The explorer may still choose the conventional approach — that's valid. But record that the choice was made consciously, not by default

This isn't about rejecting analogy — it's about making sure it's a *deliberate* choice rather than an unexamined default. For deep or contentious cases, use dialectic stress-testing — read the relevant Socrates protocol file and execute it inline (see Setup in the main discovery skill for paths).

## Assumption Hunting

Actively listen for unstated assumptions. Common categories:
- **User behavior**: "Users will always..." / "No one would..."
- **Technical**: "The API will..." / "This fits in memory..."
- **Organizational**: "The team can..." / "Stakeholders agree..."
- **Temporal**: "This won't change..." / "We'll have time to..."

When you spot one: "I notice we're assuming [X]. Should I record that as an assumption to verify, or is it a known fact?"

## Contradiction Detection

Track the decision log mentally. When a new statement conflicts with an earlier decision:
- Surface it immediately but neutrally: "This might conflict with D3 where we said [X]. Is this a revision, or am I misreading?"
- If it's a revision: update D3 as superseded, create new decision
- If it's a misread: clarify and record the clarification

## Periodic Synthesis

Every ~10 decisions or at natural topic transitions:
1. Pause and write a checkpoint summary in the notes file
2. Share the synthesis with the explorer: "Here's where I think we are — [narrative summary]. Anything off?"
3. This catches drift, misunderstandings, and forgotten threads

## Scope Management

When the explorer starts expanding scope mid-session:
- Name it: "This sounds like it might be a new topic — should we park it or explore now?"
- If exploring: update the Scope section in the notes
- If parking: add to Parking Lot with context

## Decision Confidence Calibration

Not every "I think" is the same confidence. When recording decisions, calibrate:
- Explorer sounds certain → confirm: "Recording as firm — committed?"
- Explorer hedges ("probably", "maybe", "for now") → "I'll mark this tentative — revisit if we learn more?"
- Explorer is blocked on another question → "Marking provisional until Q# is resolved"
