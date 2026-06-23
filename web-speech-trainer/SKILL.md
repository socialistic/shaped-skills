---
name: web-speech-trainer
description: >
  Simulate the OSLL Web Speech Trainer's presentation analysis pipeline.
  Accept a presentation file and speech transcript (or recording notes),
  then evaluate the delivery against configurable criteria packs covering
  pace, filler words, duration, slide quality, and speech-to-slide alignment.
---

# Web Speech Trainer — Presentation Delivery Analysis

Provenance: shaped from [OSLL/web_speech_trainer](https://github.com/OSLL/web_speech_trainer),
a 6-year, 850+ commit JavaScript/Python web application for evaluating
academic and professional presentations. The original repo is a full-stack
Docker deployment with no agent-compatible SKILL.md; this skill distills
its criteria engine into a standalone analysis workflow.

Source URL: https://github.com/OSLL/web_speech_trainer

## What This Skill Does

Replicate the core evaluation loop of the Web Speech Trainer:

1. Accept the user's presentation slides and speech transcript/notes
2. Run each applicable criterion from the trainer's analysis framework
3. Return a structured feedback report with per-criterion verdicts

## Inputs

- **Presentation file** — PDF, PPTX, or ODP slides (or a text description
  of slide contents if the file cannot be parsed directly)
- **Speech transcript or delivery notes** — full text of what was spoken,
  or timing/pacing notes from a rehearsal

Optional:
- **Target duration** — expected presentation length in minutes (default: 10)
- **Criteria pack** — which evaluation criteria to apply (default: all)
- **Language** — presentation language for filler-word detection (default: en)

## Criteria Framework

Evaluate the presentation against these criterion families, modeled on the
original [criteria engine](https://github.com/OSLL/web_speech_trainer/tree/master/app/criteria):

### Speech Metrics
| Criterion | What it checks | Reference |
|---|---|---|
| Speech Duration | Total speaking time vs. target window | [speech_duration/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/speech_duration/criterion.py) |
| Strict Speech Duration | Hard min/max enforcement | Same module, strict variant |
| Speech Pace | Words per minute within acceptable range | [speech_pace/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/speech_pace/criterion.py) |
| Fillers Count | Absolute number of filler words | [fillers_number/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/fillers_number/criterion.py) |
| Fillers Ratio | Filler words as proportion of total words | [fillers_ratio/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/fillers_ratio/criterion.py) |

### Slide Quality
| Criterion | What it checks | Reference |
|---|---|---|
| Words Per Slide | Minimum word count on each slide | [number_word_on_slide/critetion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/number_word_on_slide/critetion.py) |
| Text Length Per Slide | Character density on each slide | [len_text_on_slide/critetion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/len_text_on_slide/critetion.py) |
| Number of Slides | Slide count within range (3–15) | [number_slides/critetion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/number_slides/critetion.py) |
| Slides Checker | Structural/academic formatting validation | [slides_checker/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/slides_checker/criterion.py) |

### Content Alignment
| Criterion | What it checks | Reference |
|---|---|---|
| Speech vs. Slides | Per-slide alignment between spoken and written content | [comparison_speech_slides/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/comparison_speech_slides/criterion.py) |
| Whole Speech Coherence | Overall semantic consistency of the full talk | [comparison_whole_speech/criterion.py](https://github.com/OSLL/web_speech_trainer/blob/master/app/criteria/comparison_whole_speech/criterion.py) |

## Evaluation Procedure

For each applicable criterion:

1. Extract the relevant signal from the inputs (word count, slide text,
   timing, transcript tokens)
2. Apply the criterion's threshold or comparison logic
3. Produce a verdict: PASS, WARN, or FAIL with a one-line explanation
4. When a criterion fails, include a concrete suggestion for improvement

## Output Contract

Return a JSON object:

```json
{
  "summary": "Overall presentation readiness assessment",
  "criteria_results": [
    {
      "criterion": "speech_pace",
      "verdict": "PASS | WARN | FAIL",
      "detail": "130 wpm — within target range (120–150)",
      "suggestion": "Optional improvement note"
    }
  ],
  "strengths": ["List of strong points"],
  "improvements": ["Prioritized action items"],
  "estimated_duration_minutes": 12.5,
  "filler_count": 8,
  "slide_count": 14,
  "words_per_minute": 130
}
```

## Constraints

- Do not fabricate metrics. If the input lacks timing data, state that
  duration/pace criteria cannot be evaluated and skip them.
- Filler word lists vary by language. For English, flag: um, uh, like,
  you know, basically, actually, literally, so, right, I mean.
- Slide-count and word-per-slide criteria require actual slide content
  or a structured description of each slide.
- This skill simulates the trainer's criteria engine; it does not run
  the original Docker application or access its database.
