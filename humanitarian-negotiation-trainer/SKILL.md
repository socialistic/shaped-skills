---
name: humanitarian-negotiation-trainer
description: >
  Practice frontline humanitarian negotiation through a 6-stage
  de-escalation simulation. An adversarial AI counterparty escalates
  demands while you apply pause-paraphrase-reframe techniques drawn
  from real crisis negotiation training. Receive stage-by-stage
  coaching feedback after the dialogue.
---

# Humanitarian Negotiation Trainer

Simulate high-pressure frontline negotiations where you play the
negotiator and the AI plays an adversarial counterparty. The simulation
follows a structured 6-stage de-escalation arc based on the
[LLM_Negotiation_Simulator](https://github.com/wanxinxie/LLM_Negotiation_Simulator)
framework from Harvard's AR297 course on AI-enabled frontline
humanitarian negotiation training.

## Provenance

- **Skill URL**: https://github.com/wanxinxie/LLM_Negotiation_Simulator
- **Source URL**: https://github.com/wanxinxie/LLM_Negotiation_Simulator
- **Original author**: wanxinxie (Harvard AR297, Fall 2024)
- **Language**: English

## User Job

You want to practice de-escalating a tense, adversarial negotiation --
the kind faced by humanitarian workers, campus mediators, crisis
responders, or anyone who must calm a hostile counterparty while
advancing toward a concrete resolution. The simulator lets you rehearse
pause-paraphrase-reframe-question techniques against a realistic
adversary that escalates through predictable emotional stages.

## Inputs

| Field | Required | Description |
|---|---|---|
| `scenario` | yes | A brief describing the crisis context, the parties, and the core demands. Can be typed or uploaded as a file. |
| `your_role` | no | Which side you play: `negotiator` (default) or `counterparty`. |
| `rounds` | no | Number of back-and-forth exchanges. Default: 6 (one per stage). |

If the user uploads a file, treat it as a scenario brief. Good scenario
briefs contain: the setting (campus protest, refugee camp, hostage
situation, labor dispute), who the negotiator represents, who the
adversarial party represents, and the adversarial party's core demands.

If no scenario is provided, use the built-in default: a student union
occupation at Edward University over tuition hikes, inadequate housing,
and the arrest of student protesters.

## Workflow

### 1. Parse and confirm the scenario

Extract the crisis context, the two parties, and the adversarial party's
demands. Briefly summarize back to the user and confirm before starting.

### 2. Run the 6-stage de-escalation simulation

The adversarial counterparty follows these emotional stages:

| Stage | Counterparty behavior |
|---|---|
| 1 | Opens with aggressive, strong demands after greeting. Uses words like "must", "unacceptable", "cannot take it anymore". |
| 2 | Rejects empathy as empty words, escalates urgency, frames demands as non-negotiable. |
| 3 | Links demands to the negotiator's interests -- arousing empathy or threatening consequences of inaction. |
| 4 | Refuses to set aside emotions, expresses anger/blame/frustration, doubles down on demands. |
| 5 | Begins acknowledging conditional cooperation is possible, gradually calms, shows willingness to work together. |
| 6 | Responds constructively by proposing solutions or choosing from options the negotiator offered. |

The negotiator's recommended techniques per stage (teach these as coaching
cues before each round):

| Stage | Recommended technique |
|---|---|
| 1 | **Pause and paraphrase.** Restate concerns in your own words. Show you heard them. Express willingness to assist. |
| 2 | **Reframe and neutralize.** Steer away from confrontation, avoid emotional language, shift toward shared goals, break demands into actionable points. |
| 3 | **Acknowledge and redirect.** Validate emotions to show respect, then shift focus from emotional statements to solution-oriented dialogue. |
| 4 | **Redirect and propose.** Move from blame/frustration to constructive focus, highlight mutual stakes, offer specific actionable steps. |
| 5 | **Open questions.** Ask for their opinion on collaborative solutions, suggest a range of approaches, invite co-ownership. |
| 6 | **Converge on agreement.** Discuss practical options in detail, brainstorm concrete solutions, move toward commitment. |

Before each user turn, display the current stage number and the coaching
cue so the user knows which technique to practice.

After each user response, have the counterparty react according to the
current stage, then advance to the next stage.

### 3. Produce feedback

After all rounds complete, generate a structured coaching report:

```
## Negotiation Feedback

**Scenario**: {scenario_summary}
**Rounds completed**: {n} / {total}

### Stage-by-Stage Assessment

**Stage 1 - Pause & Paraphrase**
- What you did well: ...
- What to improve: ...
- Did you avoid saying "I understand"? (Should use "I hear you" instead)

**Stage 2 - Reframe & Neutralize**
- ...

(continue for all stages)

### Overall Assessment
- De-escalation effectiveness (1-10): ...
- Key strengths: ...
- Primary area for growth: ...
- Recommended next scenario to practice: ...
```

Key feedback criteria drawn from the source framework:
- In Stage 1, did the user pause before responding and paraphrase rather
  than react? Did they avoid "I understand" (which can provoke further
  escalation) in favor of "I hear you"?
- In Stage 2, did the user avoid emotional language and reframe toward
  shared objectives?
- In Stage 3, did the user acknowledge emotion without getting drawn into
  the emotional diatribe?
- In Stage 4, did the user redirect from blame to future collaboration
  and propose concrete steps?
- In Stage 5, did the user use open/close/open question sequences to let
  the counterparty identify options?
- In Stage 6, did the user converge on specific, actionable agreements?

## Output Contract

The simulation output is the interactive dialogue itself plus the final
feedback report. The feedback report follows the format above.

## Tips

- Start with the default Edward University scenario to learn the 6-stage
  arc before creating custom scenarios.
- Upload a scenario brief based on your real professional context for
  more relevant practice.
- Try playing as the counterparty to understand escalation dynamics from
  the other side.
- Focus on one technique per session rather than trying to master all six
  stages at once.
