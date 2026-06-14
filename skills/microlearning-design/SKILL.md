---
name: microlearning-design
description: Designing Microlearning Sessions with AI. Use this skill when an L&D professional, instructional designer, or trainer wants to design a short, focused learning moment (3–10 minutes) — for mobile, LMS, onboarding, performance support, or just-in-time training. Trigger when someone says "I need something short", "make this into a microlearning", "design a 5-minute module", "create a quick training", "just-in-time learning", or wants to use AI to plan and script a focused learning experience. This skill designs the STRUCTURE — what scenes, what sequence, what objective, what practice moment — not just the content text.
---

# Designing Microlearning Sessions with AI

Most "microlearning" is just short — not focused. A real microlearning session targets one behavior change, structures content to produce it, and fits in a gap between other work. This skill walks you through designing one in under an hour.

## Step 1 — Nail the focus

Ask the user:

1. **What's the one behavior change this microlearning produces?** If they give you a topic ("GDPR"), push for a behavior: "After watching this, what will the learner do differently on Monday morning?"
2. **Who is the learner?** (role, context, how they'll access this — phone, desktop, LMS, standalone link)
3. **How much time does the learner have?** Ideal = 3–7 minutes. If they say "15 minutes", ask: "Could this be two 7-minute modules?"
4. **Is this standalone or part of a series?** (affects how much context to assume)
5. **Source content?** (existing text, a policy, a training transcript, or building from scratch)

## Step 2 — Apply the microlearning formula

Three non-negotiables for effective microlearning:

| Element | Why it matters |
|---------|---------------|
| **One objective** | Cognitive load kills retention — one clear target beats three vague ones |
| **One behavior change** | "Learners will understand X" is not a behavior. Understanding isn't visible. |
| **One moment of practice** | Even 30 seconds of retrieval doubles retention vs. passive consumption |

Formalize the focus before designing anything:

```
MICROLEARNING FOCUS STATEMENT

Objective: [Action verb] + [what the learner does] + [in what situation]
Behavior change: On [day/situation], the learner will [do something differently] instead of [current behavior]
Practice moment: [What they do to activate the learning — decide, apply, reflect, produce]
```

Show this to the user and confirm before building the structure.

## Step 3 — Generate the scene structure with AI

Use this prompt with the user's confirmed inputs:

```
MICROLEARNING STRUCTURE PROMPT

Role: You are an instructional designer specializing in microlearning.
Behavior change: After this session, [target learner] will [specific behavior] when [situation].
Learner context: [role, what they already know, access method]
Time budget: [X minutes maximum]
Source content: [paste content or describe topic]

Design a microlearning structure:
- Total scenes: 3–5 (maximum)
- Scene 1 (Hook, ~45 sec): Opens with why this matters RIGHT NOW to the learner. A consequence, a scenario, or a provocative question. No definitions, no "in this module you will learn".
- Scene 2–3 (Concept, ~90 sec each): The essential core only — one idea per scene, shown through a specific example relevant to the learner's job. Max 80 words of prose per scene.
- Scene 4 (Practice, ~60 sec): A decision or recall moment — not a memory test, but something that requires applying the concept. Include 2 sentences explaining why the correct answer is correct.
- Scene 5 (Summary + action, ~30 sec): 2 bullets: what was learned + one specific thing to do or notice next time. No new information.

For each scene output:
- Scene title (5 words max)
- Core content (max 80 words)
- Visual or layout suggestion
```

Show the generated structure to the user and calibrate before scripting.

## Step 4 — Script the two hardest scenes

Offer to fully script the Hook and Practice scenes, since those are where most microlearning fails:

**Hook**: must create a feeling — urgency, curiosity, recognition — in under 45 seconds. Ask: "What's the worst thing that happens when a learner gets this wrong? Start there."

**Practice**: the question must require applying the concept, not just recognizing a word from the text. A good practice question has a tempting wrong answer.

## Step 5 — The microlearning design prompt pack to take away

```
MICROLEARNING DESIGN PROMPT PACK

--- Full structure generator ---
Role: Expert microlearning designer.
Behavior change: After this, [learner role] will [behavior] when [situation].
Context: [role, access method, time budget].
Source: [paste content].
Generate a 3–5 scene structure: Hook → Concept(s) → Practice → Summary.
Per scene: title (5 words max), content (80 words max), visual suggestion.

--- Hook writer ---
Write an opening scene for a microlearning on [topic] aimed at [learner].
The hook must: create immediate relevance, skip definitions and preamble, use a scenario or consequence.
Max 60 words. The learner should feel "this is about me" within 10 seconds.

--- Practice question generator ---
I'm teaching [concept] to [learner role].
Write a practice question that requires applying [concept], not just recognizing it.
Format: brief situation → one decision to make.
Include 3 answer options and a 2-sentence explanation of why the correct answer is correct.

--- Behavior change test (apply to any draft) ---
Read this microlearning content. For each scene, answer:
1. What is the learner doing — consuming or applying?
2. Could a learner complete this scene without thinking?
3. After finishing, what would they do differently at work?
If scene 1 or 2 answers "consuming" or "yes" to Q2, rewrite to increase active engagement.
```
