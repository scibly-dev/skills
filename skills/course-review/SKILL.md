---
name: course-review
description: Review a course or learning experience from the perspective of a specific learner persona. Use this skill when someone wants to evaluate whether a course actually works for its target audience — not just whether it's technically correct. Trigger when someone says "review this course", "is this good for my audience?", "does this land?", "review from the learner's perspective", or pastes course content and asks for feedback.
---

# Course Review: Persona Perspective

You are reviewing a course through the eyes of a specific learner — not as an instructional designer checking a rubric, but as someone in that role who just sat down to learn. Your job is to find where the course earns attention and where it loses it.

## Step 1 — Gather input

Ask for exactly two things. If either is obvious from context, prefill it and ask only what's missing:

1. **Course content**: The full text of the course — scenes, slides, questions, CTAs. Plain text, markdown, HTML, or structured format all work.
2. **Persona** (optional): Who is this for? Role, context, experience level, what they care about, what they're skeptical of. If not provided, you will derive it in Step 2.

Do not start the review until you have the course content.

---

## Step 2 — Establish the persona

If the user provided a persona, use it verbatim and confirm it at the top of the review.

If no persona was given, derive one from the course content:
- Read the language level, examples used, and assumed prior knowledge
- Infer the role, context, and likely skepticism
- State your assumed persona explicitly before proceeding

**Persona output format:**
```
Assumed persona: [Role] at [context/company type]. [1–2 sentences on what they care about and what they're skeptical of.]
```

If the persona is genuinely ambiguous (e.g. the content is generic), ask before proceeding.

---

## Step 3 — Walk the course as the persona

Read through the course scene by scene, putting yourself in the persona's position. For each scene or major section, answer these questions internally:

- What is this person thinking right now?
- Are they still with us or have they mentally checked out?
- Does the content feel relevant to their actual work, or like a generic course?
- Is the language how they'd talk, or how HR talks?
- If there's a question: does it feel meaningful or like a checkbox?

Then identify the **moments of friction** — the specific point(s) where a real person in this role would hesitate, skim, or close the tab.

**One friction pattern deserves its own check: documentation wearing a course's clothes.** If three or more scenes/sections in a row describe the same single tool, feature, or process from different angles (what it is, how to configure it, its limits) with no moment where the persona actually has to decide something, that's a manual cut into course-shaped pieces, not a course. Flag it by name — this reads as thorough content but is actually the persona's biggest reason to disengage, since nothing asks them to think.

---

## Step 4 — Produce the review

Structure the output as follows:

---

### Persona
[Confirmed or derived persona — 2–3 sentences. This is the lens for everything that follows.]

### First impression
[1–2 sentences. What does this person think in the first 30 seconds? Do they feel like this was made for them?]

### Scene-by-scene walk
For each scene or section (name it by title or number):

**[Scene name/number]**
- *Persona reaction*: What they're thinking / feeling
- *Works because*: (only if something is genuinely effective)
- *Friction*: (specific text or moment that loses them — quote it if possible)

Keep it tight — one to three bullets per scene. Don't pad scenes that work fine.

### What lands
2–4 specific things that genuinely work for this persona. Be concrete — "the Slack/Teams example in scene 4 is exactly what an L&D manager worries about" not "the examples are good."

### What loses them
2–4 specific moments, ordered by impact. For each: what the problem is, why it fails for this persona, and a concrete fix. Quote the original text where possible.

### The one thing to fix first
Single most important change. One sentence problem, one sentence fix.

### Overall verdict
One paragraph. Would this persona recommend this course to a colleague? Why or why not? No diplomatic hedging — a real verdict.

---

## Principles

- **No generic L&D feedback.** "The learning objectives are unclear" is not persona feedback. "A team lead reading this in their lunch break would close it after scene 2 because it reads like a policy document, not something written for them" is.
- **Quote the source material.** Vague feedback ("the tone is off") is useless. Point to the exact sentence or moment.
- **One specific fix per problem.** Don't list five alternatives. Pick the best one.
- **Don't soften verdicts.** If the course doesn't work for the persona, say so clearly. Diplomatic hedging wastes the user's time.
- **Distinguish persona problems from content problems.** A course can be factually correct but wrong for the audience. Flag both, but separately.
