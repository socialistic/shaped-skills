---
name: ai-voice-agent-roleplay
description: |
  Build and customize a real-time AI voice agent for sales roleplay,
  customer-support practice, or phone-reception scenarios using Deepgram
  STT/TTS and OpenAI. Based on the open-source AI-Voice-Agent project.
---

# AI Voice Agent — Sales Roleplay & Support Practice

Turn the open-source [AI-Voice-Agent](https://github.com/Anil-matcha/AI-Voice-Agent)
into a tailored real-time voice agent for your sales training, customer-support
drill, or phone-reception scenario.

**Provenance**

- Source repository: <https://github.com/Anil-matcha/AI-Voice-Agent>
- Skill URL: <https://github.com/Anil-matcha/AI-Voice-Agent>
- 145 stars, Python, Deepgram + OpenAI stack
- YouTube walkthrough: <https://youtu.be/77xnx26dyYU>

## What This Skill Does

Given a scenario brief (industry, role, menu/catalog, objection style), this
skill generates a complete, ready-to-run voice-agent configuration:

1. **System prompt** — a persona prompt tuned to the target scenario (sales
   closer, restaurant host, SaaS demo rep, complaint handler, etc.) with
   style guardrails, ASR-error recovery, and conversation-flow rules.
2. **Menu / catalog payload** — structured item list with names, quantities,
   and prices (or feature tiers, objection playbooks) embedded in the prompt.
3. **Deepgram + OpenAI wiring guidance** — model selection (Nova-2 for STT,
   Aura Helios for TTS, GPT model for chat), env-var setup, and
   `LiveOptions` tuning for the target language and latency profile.
4. **Evaluation criteria** — a concise rubric the user can apply after a
   practice call to score fluency, objection handling, upsell attempts, and
   closing technique.

## Inputs

| Input | Required | Description |
|---|---|---|
| Scenario brief | Yes | Free-text description of the voice-agent role: industry, persona name, task (reservation, cold call, demo, complaint), tone, and any special rules. |
| Menu / catalog | No | Structured list of items, prices, tiers, or objection cards. If omitted, the skill generates a plausible sample for the stated industry. |
| Target language | No | Defaults to `en-US`. Affects Deepgram model and TTS voice selection. |
| Difficulty level | No | Easy / Medium / Hard — controls how cooperative or adversarial the simulated caller behaves. |

## Workflow

### Step 1 — Understand the scenario

Read the user's scenario brief. Identify:
- **Role**: who the voice agent pretends to be (receptionist, SDR, account exec, support tier-1)
- **Counter-party**: who the caller pretends to be (prospect, angry customer, mystery shopper)
- **Success criteria**: what counts as a successful call (reservation booked, demo scheduled, complaint resolved, upsell landed)
- **Constraints**: budget caps, forbidden phrases, required disclosures, menu boundaries

### Step 2 — Generate the system prompt

Produce a system prompt following the structure proven in the source repo:

```
##Objective
##Role — Personality + Task
##Menu Items (or Catalog / Objection Playbook)
##Conversational Style
##Response Guideline — ASR error handling, role adherence, smooth flow
##Style Guardrails — conciseness, no repetition, conversational tone, emotions, proactivity
```

Customize every section to the user's scenario. Do not reuse the restaurant
example verbatim; adapt structure and guardrails to the stated domain.

### Step 3 — Recommend technical config

Based on the scenario, recommend:
- Deepgram STT model and language code
- Deepgram TTS voice (from Aura lineup) matching the persona's gender/tone
- OpenAI chat model (gpt-4o-mini for low-latency, gpt-4o for complex negotiation)
- `LiveOptions` parameters: `utterance_end_ms`, `endpointing`, `vad_events`
- Env vars needed: `DEEPGRAM_API_KEY`, `OPENAI_API_KEY`

### Step 4 — Provide evaluation rubric

Generate a 5-point rubric the user can score after each practice call:
1. **Greeting & rapport** — did the agent open naturally?
2. **Discovery questions** — did it ask targeted questions?
3. **Objection handling** — did it address pushback without breaking character?
4. **Upsell / cross-sell** — did it spot and act on opportunities?
5. **Close & next steps** — did it land a clear commitment or follow-up?

Adjust rubric categories to the scenario (support calls replace upsell with
resolution quality; reservation calls replace discovery with preference capture).

## Output

Return to the user:
1. The full system prompt (copy-paste ready for `app.py`)
2. A recommended `.env.example` with model and voice selections
3. The evaluation rubric as a markdown checklist
4. A one-paragraph summary of how to run the agent locally

## References

- Source `app.py` with full Deepgram/OpenAI wiring: <https://github.com/Anil-matcha/AI-Voice-Agent/blob/main/app.py>
- Setup and requirements: <https://github.com/Anil-matcha/AI-Voice-Agent/blob/main/requirements.txt>
- Video tutorial: <https://youtu.be/77xnx26dyYU>
- Medium guide: <https://medium.com/@anilmatcha/ai-voice-agent-how-to-build-one-in-minutes-a-comprehensive-guide-032a79a1ac1e>
