---
name: PDF MCQ Paper Generator
description: |
  Turn an educational PDF into a complete multiple-choice question paper
  with adjustable Easy/Medium/Hard distribution and a separate answer key.
  Upload a textbook chapter or lecture PDF; get two print-ready PDFs back.
original_repo: tnagendran81/ai-based-mcq-generator
original_author: tnagendran81
original_url: https://github.com/tnagendran81/ai-based-mcq-generator
conformed_by: socialistic.ai
conformed_at: 2026-06-18
---

# PDF MCQ Paper Generator

Source: https://github.com/tnagendran81/ai-based-mcq-generator
Discovered via: https://medium.com/@tnagendran.81/when-ai-meets-real-teaching-building-an-mcq-generator-that-actually-gets-it-f347c1f3bdbe

Generate professional multiple-choice question papers from educational PDF
content. Designed for educators, trainers, and exam authors who need to
produce assessments quickly from existing course material.

## What It Does

Given a PDF of educational content (textbook chapter, lecture slides, course
notes), this skill:

1. Extracts text and images from the PDF
2. Generates MCQs at three complexity tiers:
   - **Easy** — basic facts, definitions, direct recall
   - **Medium** — relationships, application, conceptual understanding
   - **Hard** — analysis, evaluation, synthesis, critical thinking
3. Distributes questions according to the requested Easy/Medium/Hard
   percentage split (default 40/40/20)
4. Produces two outputs:
   - **Student question paper** — numbered questions with four choices (A–D),
     no answers shown
   - **Educator answer key** — correct answers with explanations for each
     question

## Inputs

- **PDF file** (required) — the educational content to generate questions
  from. Max ~50 pages of text-based content.
- **Number of questions** (optional) — how many MCQs to generate (5–50,
  default 10).
- **Page range** (optional) — which pages to use from the PDF.
- **Complexity distribution** (optional) — percentage split across
  Easy / Medium / Hard difficulty. Values are normalized to 100%.

## Output

Two documents:

1. A **question paper** with numbered MCQs, four options each, formatted for
   print or digital distribution
2. An **answer key** with the correct option and a brief explanation for each
   question

## How to Use

1. Upload your educational PDF (textbook chapter, lecture notes, course
   handout).
2. Specify how many questions you want and the desired difficulty mix:
   - Example: "Generate 20 MCQs from this chapter, 30% easy / 50% medium /
     20% hard"
   - Example: "Make a 15-question quiz from pages 5–12, mostly medium
     difficulty"
3. Receive the question paper and answer key.

### Example Prompts

- "Generate a 25-question exam from this biology chapter PDF with 40% easy,
  40% medium, 20% hard questions"
- "Create 10 MCQs from this history lecture, all medium difficulty"
- "Make a quick 5-question quiz from this math textbook section"
