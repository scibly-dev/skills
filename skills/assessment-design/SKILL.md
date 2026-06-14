---
name: assessment-design
description: Assessment Design with AI for L&D teams. Use this skill when an instructional designer or course developer wants to create quiz questions, knowledge checks, assessments, or test items using AI. Especially useful when going beyond simple MCQs — covering application-level questions, scenario-based items, distractors that actually work, and meaningful feedback. Trigger when someone asks for help writing questions, assessments, quizzes, or wants to make evaluations more rigorous. Also trigger when someone says their AI-generated questions are "too easy", "too obvious", or "just testing recall".
---

# Assessment Design with AI

Most AI-generated questions are too easy — they test recognition, not learning. This skill shows you how to use Bloom's taxonomy as a prompt lever to get application-level questions with quality distractors and feedback that actually teaches.

## Step 1 — Understand the assessment context

Ask the user:

1. **What's the topic?** Be specific — not "leadership", but "giving constructive feedback to a peer who missed a deadline".
2. **What Bloom's level?** If unsure:
   - *Remember / Understand*: learners recall or explain concepts → compliance or foundational knowledge
   - *Apply*: learners use knowledge in a new situation → skills and procedures
   - *Analyze / Evaluate*: learners break down situations or make judgments → complex decision-making
3. **Question format?** MCQ, scenario-based (situation + question), true/false with explanation, short answer, matching, drag-to-sequence.
4. **How many questions?** And for what purpose — formative check, end-of-course test, certification?
5. **What does the learner already know?** Helps calibrate how tricky the distractors should be.

## Step 2 — Build the assessment generation prompt

Construct this prompt:

```
ASSESSMENT GENERATION PROMPT

Role: You are an expert assessment designer and instructional design specialist.
Topic: [specific topic]
Target Bloom's level: [Remember / Understand / Apply / Analyze / Evaluate]
Learner: [role + experience level]
Question format: [MCQ / scenario-based / true-false / matching]
Number of questions: [N]

For each question:
- Write a stem that presents a realistic situation or judgment call — avoid "which of the following" where possible.
- [If MCQ] Write 4 answer options: 1 correct, 2 plausible distractors reflecting common mistakes, 1 tempting shortcut with a hidden flaw.
- [If scenario-based] Start with a 2-sentence situation before the question.
- Write feedback for each option: why it's correct or why it's a common mistake (2 sentences max per option).
- Tag each question with its Bloom's level.

Avoid: trivially obvious wrong answers, questions answerable by scanning the course text without thinking, trick questions, double negatives.
```

Run it if the user wants to see output.

## Step 3 — Quality check the output

Review generated questions against these criteria and flag issues:

| Check | What to look for |
|-------|-----------------|
| Distractors are plausible | Wrong answers reflect real misconceptions, not obvious nonsense |
| Stem is unambiguous | Learner knows exactly what's being asked without re-reading |
| Bloom's level is correct | Apply questions require doing something, not just recognizing a term |
| Feedback teaches | Correct answer feedback explains WHY, not just "Correct!" |
| No giveaways | The right answer isn't the longest, most formal, or most cautious option |

Offer to revise questions that don't pass.

## Step 4 — Assessment prompt toolkit to take away

```
ASSESSMENT PROMPT TOOLKIT

--- For application-level MCQs ---
Write [N] multiple choice questions at the Apply level for [topic].
Learner: [role + experience level].
Each question: realistic situation stem, 4 options (1 correct, 2 plausible-wrong reflecting common mistakes, 1 tempting shortcut), feedback per option explaining why.
Avoid obvious distractors and trick questions.

--- For scenario-based questions ---
Write [N] scenario-based questions for [topic].
Each: 2-sentence realistic situation, then a decision or judgment question.
Options: [format]. Include per-option feedback.

--- For better distractors (fix existing questions) ---
I have this question: [paste question + correct answer].
Write 3 distractors that reflect genuine misconceptions a learner at [level] might have.
Each distractor: plausible, specific, reflects a real mistake — not obviously wrong.

--- For feedback that teaches ---
I have this question and answer: [paste].
Write feedback for each option that explains the reasoning, not just whether it's right or wrong.
Correct answer feedback: explain why this is the right call.
Wrong answer feedback: explain the common thinking error, then redirect.
```
