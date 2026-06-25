---
name: llm-debate
description: |
  Orchestrate a structured turn-by-turn debate between two LLMs on any topic.
  Assign opposing positions, run configurable rounds, and optionally judge the
  outcome with a scorecard across argumentation, evidence, rebuttal, clarity,
  and persuasiveness.
---

# LLM Debate — Structured Multi-Model Argumentation

Provenance: shaped from [sajal2692/llm-debate](https://github.com/sajal2692/llm-debate),
discovered via [Reddit r/vibecoding](https://www.reddit.com/r/vibecoding/comments/1rn24ll/i_built_a_web_app_that_pits_two_llms_against_each/).

## What This Skill Does

You orchestrate a full debate between two LLM personas. Given a topic and two
opposing positions, you simulate each debater in alternating turns, then
optionally act as a judge scoring both sides. The output is a structured debate
transcript with an optional verdict scorecard.

This replicates the core workflow of the llm-debate web app as a portable,
text-based skill: no server, no OpenRouter key, no UI required.

## Inputs

The user provides:

| Input | Required | Default |
|---|---|---|
| **topic** | yes | — |
| **pov_a** | no | auto-generate an affirmative position |
| **pov_b** | no | auto-generate an opposing position |
| **max_turns** | no | 3 (each turn = both sides speak once) |
| **judge** | no | true — produce a verdict scorecard |

If the user uploads a file (article, paper excerpt, notes), treat its content as
context that frames the debate topic.

## Workflow

### Step 1 — Frame the Debate

If the user supplied only a topic without explicit positions:

1. Generate two clear, opposing points of view (POV A and POV B).
2. Present them for confirmation before proceeding.

If positions are provided, confirm the setup and proceed.

### Step 2 — Run the Debate

For each turn (1 through `max_turns`):

1. **Side A speaks.** Adopt Side A's persona fully. Argue from their position,
   referencing the topic context and rebutting Side B's prior arguments (if any).
   Write 150-300 words per turn.

2. **Side B speaks.** Adopt Side B's persona fully. Counter Side A's points,
   advance new arguments, and defend their position. Write 150-300 words per
   turn.

Rules for each debater persona:
- Stay in character for the assigned position throughout.
- Reference and directly rebut specific points the opponent made.
- Introduce new evidence or reasoning each turn; do not repeat arguments.
- Maintain a structured, persuasive rhetorical style.

Format each turn clearly:

```
## Turn N

**[Side A — Position Name] (Model A)**
[argument text]

**[Side B — Position Name] (Model B)**
[argument text]
```

### Step 3 — Judge (Optional)

If judging is enabled (default), evaluate the completed debate as a neutral
third-party judge. Score each side on five facets (0-10 each):

| Facet | What it measures |
|---|---|
| Argumentation | Logical structure, coherence of reasoning |
| Evidence & Reasoning | Quality of supporting evidence and examples |
| Rebuttal | Effectiveness of countering opponent's points |
| Clarity | Clear expression, readability, organization |
| Persuasiveness | Overall convincing power and rhetorical impact |

Produce a verdict with:
- Per-facet scores and brief notes for each side
- Total scores (max 50 per side)
- Winner declaration (or Draw)
- A 2-3 sentence summary explaining the decision

Format the verdict as a structured scorecard table.

## Output

Return the full debate transcript followed by the judge verdict (if enabled).
The transcript should be readable as a standalone document.

## References

- Source repo: https://github.com/sajal2692/llm-debate
- Debate logic: https://github.com/sajal2692/llm-debate/blob/main/backend/debate.py
- Judge scoring system: 5 facets x 10 points, structured JSON scorecard
- Original app: React + FastAPI with SSE streaming via OpenRouter
