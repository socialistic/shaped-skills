---
name: ai-debates
description: |
  Orchestrate structured multi-round debates between AI models with custom
  personas, moderator-guided flow, and automated verdicts.  Based on the
  open-source AI Debates platform (https://github.com/khoren93/ai-debates).
---

# AI Debates — Multi-Model Debate Orchestrator

Run a structured debate between two or more AI personas on any topic.  A
moderator AI guides the discussion through opening statements, rebuttals,
and closing arguments, then delivers a final verdict with fallacy analysis.

Provenance: shaped from [khoren93/ai-debates](https://github.com/khoren93/ai-debates),
a FastAPI + React platform that orchestrates real-time streaming debates
across 600+ LLMs via OpenRouter.

## Inputs

The user provides:

| Input | Required | Description |
|---|---|---|
| **topic** | yes | The debate proposition or question |
| **description** | no | Additional context or framing for the topic |
| **debater_1** | yes | Name, persona/stance, and preferred model for side A |
| **debater_2** | yes | Name, persona/stance, and preferred model for side B |
| **moderator** | no | Name and model for the moderator/judge (default: auto) |
| **num_rounds** | no | Number of argument rounds (default: 3) |
| **intensity** | no | Rhetorical intensity 1-10 (1 = calm academic, 10 = passionate; default: 5) |
| **language** | no | Output language (default: English) |
| **length** | no | Response length per turn: very_short / short / medium / long (default: medium) |

The user may also upload reference material (articles, position papers, prior
debate transcripts) as context for the debaters.

## Workflow

### 1. Configure the debate

Parse the user's topic, participant descriptions, and settings.  For each
debater, build a persona prompt that captures their assigned stance, voice,
and knowledge constraints.  For the moderator, prepare a system prompt that
enforces debate rules and turn structure.

Confirm the configuration with the user before proceeding.

### 2. Run debate rounds

For each round, execute this cycle:

1. **Moderator introduction** — frame the round, recap key points, pose the
   next question or angle.
2. **Debater A argues** — present or rebut within the word limit, staying in
   persona.
3. **Debater B argues** — respond to Debater A, advance own position.

Stream each turn to the user as it is generated.  Maintain the full
transcript as context for subsequent turns.

Intensity levels guide rhetorical style:
- 1-3: polite, academic, measured
- 4-6: firm, engaging, persuasive
- 7-10: passionate, dramatic, intense but respectful

Length presets control word targets per turn:
- very_short: ~50 words
- short: ~100 words
- medium: ~250 words
- long: ~500+ words

### 3. Deliver the verdict

After the final round, the moderator switches to judge mode and produces a
structured verdict:

1. **Winner** — declare a winner or draw based on argument strength, logic,
   and persuasion
2. **Analysis** — evaluate each participant's performance
3. **Key Arguments** — highlight the strongest points made
4. **Logical Fallacies** — identify any reasoning errors or weak arguments

Use Markdown formatting (bold, headers, lists) throughout the verdict.

### 4. Post-debate options

Offer the user:
- Export the full transcript
- Run a follow-up debate on a narrower sub-topic
- Swap models or personas and re-run
- Adjust intensity/length and replay

## Output Contract

Return the complete debate transcript followed by the structured verdict.
Each turn is labeled with the speaker name and round number.

## Source Reference

- Repository: https://github.com/khoren93/ai-debates
- Architecture: FastAPI backend with Redis queue orchestration, React
  frontend with SSE streaming
- Key source files:
  - [orchestrator.py](https://raw.githubusercontent.com/khoren93/ai-debates/main/backend/app/services/orchestrator.py) — turn sequencing, round-robin logic, verdict generation
  - [prompt_builder.py](https://raw.githubusercontent.com/khoren93/ai-debates/main/backend/app/services/prompt_builder.py) — persona and intensity prompt construction
  - [schemas.py](https://raw.githubusercontent.com/khoren93/ai-debates/main/backend/app/schemas/schemas.py) — debate config, participant, and response schemas
  - [CreateDebate.tsx](https://raw.githubusercontent.com/khoren93/ai-debates/main/frontend/src/pages/CreateDebate.tsx) — debate setup UI
  - [DebateLive.tsx](https://raw.githubusercontent.com/khoren93/ai-debates/main/frontend/src/pages/DebateLive.tsx) — real-time streaming debate view
