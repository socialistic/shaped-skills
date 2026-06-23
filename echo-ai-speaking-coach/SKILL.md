---
name: echo-ai-speaking-coach
description: |
  Coach a speaker through interview, presentation, or standup practice using
  Echo's real-time feedback framework: live transcription, filler-word
  detection, pace/loudness metering, on-device eye-contact tracking, and
  Gemini-powered coaching reports with STAR rewrites and 7-day improvement
  plans. Also supports mock-interview mode with role-aware question generation,
  per-answer rubric grading, and a hiring-readiness signal.
---

# Echo AI Speaking Coach

Echo is a real-time multimodal speaking coach. The user speaks into their mic
and camera; the system transcribes live, measures delivery signals (pace,
filler words, loudness, eye contact, smile), nudges gently during the talk,
and generates an AI coaching report with strengths, fixes, English coaching, a
STAR rewrite, and a 7-day improvement plan.

- Upstream source: https://github.com/poolanithinreddy/echo-ai-speaking-coach
- License: MIT

## When to route here

Use this skill when the user wants to:

- practice a job interview, presentation, or standup with real-time speaking
  feedback (pace, filler rate, eye contact, smile, loudness)
- run a mock interview with AI-generated role-aware questions and per-answer
  rubric grading
- get a structured coaching report after a practice session, including a STAR
  rewrite of their answers and a concrete 7-day plan
- set up and run the Echo local dev environment (Next.js frontend +
  Express/WebSocket backend)
- understand or customize Echo's scoring methodology (delivery, clarity,
  presence, structure weights)

Do not route here for: text-only writing feedback, pronunciation drilling
without live metrics, or coaching that does not involve real-time audio/video
capture.

## Required inputs from the user

Ask for whichever are missing before proceeding:

1. **Session type**: Interview, Presentation, or Standup.
2. **Practice material**: the talk outline, interview questions, or standup
   notes they plan to rehearse.
3. **For mock interview**: target role, difficulty level (easy/medium/hard),
   number of questions.
4. **Feedback focus** (optional): which metrics matter most — pace, fillers,
   eye contact, structure, or all.

## Architecture overview

Echo is a TypeScript monorepo (Turborepo):

- `apps/web/` — Next.js 14 frontend (App Router, Tailwind). Runs on-device
  MediaPipe face landmarks for eye-contact and smile detection; Web Audio RMS
  metering for loudness.
- `apps/api/` — Express + WebSocket backend. Streams audio to Google Cloud
  Speech-to-Text via `/ws/live/`, computes rolling WPM / filler rate / nudges
  via `/ws/coaching/`, and calls Gemini 2.5 Flash for coaching reports and
  mock-interview grading.
- `packages/core/` — shared, unit-tested scoring utilities (WPM, fillers,
  composite scoring with configurable weights).

Key source paths:

- README:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/README.md
- Scoring logic:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/packages/core/src/scoring.ts
- Gemini review service:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/apps/api/src/services/gemini-review.ts
- Mock interview service:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/apps/api/src/services/gemini-mock.ts
- Live transcription WebSocket:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/apps/api/src/ws/live.ts
- Coaching metrics WebSocket:
  https://github.com/poolanithinreddy/echo-ai-speaking-coach/blob/main/apps/api/src/ws/coaching.ts

## Scoring methodology

Final report scores are 0-100:

- **Delivery** (0.30 weight) — distance of pace from ~160 WPM target, pause
  adjusted.
- **Clarity** (0.25 weight) — penalized by filler-per-minute.
- **Presence** (0.25 weight) — eye contact (0.6) + smile (0.4).
- **Structure** (0.20 weight) — balanced length, coherent sentences,
  transitions, concrete examples, clear opening.

Canonical implementation: `packages/core/src/scoring.ts`.

## Product flows

### Live coaching session
1. Pick scenario (Interview / Presentation / Standup).
2. Grant mic + camera; audio streams for transcription; metrics update live.
3. End session: metrics scored and sent to Gemini for coaching report.

### Mock interview
1. Choose role, difficulty, number of questions.
2. Gemini generates tailored question set.
3. Answer each question with live meters running.
4. End: every answer graded against 5-dimension rubric; final report with
   hiring-readiness signal.

## Output contract

When the user invokes this skill, produce:

- a session setup plan matched to their scenario type and practice material
- real-time coaching guidance: what metrics to monitor, target thresholds
  (e.g. 130-170 WPM, < 3 fillers/min), and how to use the live nudges
- a post-session analysis framework: how to read the coaching report, which
  scores to prioritize, and how to use the STAR rewrite
- a concrete 7-day practice plan with daily focus areas derived from their
  weakest metrics
- for mock interviews: prep strategy per question, rubric dimensions to
  target, and how to interpret the readiness signal

## Setup (local development)

Prerequisites: Node.js >= 18.17, Gemini API key, optionally Google Cloud
service-account JSON for live STT.

```bash
git clone https://github.com/poolanithinreddy/echo-ai-speaking-coach.git
cd echo-ai-speaking-coach
npm run install:all
cp apps/api/.env.example apps/api/.env        # add GEMINI_API_KEY
cp apps/web/.env.example apps/web/.env.local  # API + WS URLs
npm run dev                                    # starts both api (8787) and web (3000)
```

Open http://localhost:3000 to start a session.
