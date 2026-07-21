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

Non-negotiables for effective microlearning:

| Element | Why it matters |
|---------|---------------|
| **One objective** | Cognitive load kills retention — one clear target beats three vague ones |
| **One behavior change** | "Learners will understand X" is not a behavior. Understanding isn't visible. |
| **No concept without immediate practice** | Every concept scene gets its own practice scene directly after it, not "one practice moment somewhere in the session." A concept the learner never has to use is content they will not retain, no matter how well it's explained. |
| **Resurfacing, not one-and-done** | A single pass rarely survives past the session. Plan for the core concept to reappear at least once more, in a different scene/format within the session, and ideally in a short spaced-review touchpoint days later. |
| **A decision, not a manual** | If several scenes in a row describe the same tool, feature, or process from different angles with no "given this situation, what do you do" moment, it's documentation cut into scenes, not microlearning. Every session should build toward at least one real decision. |

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
- Total scenes: scale to the content, not a fixed count. Every scene holds exactly ONE thing: one explanation OR one interaction, never both, never two ideas at once. A topic with several natural sub-parts should become several thin one-idea scenes rather than 2-3 dense ones; more, thinner scenes read like a good consumer app, fewer, denser ones read like a slide deck. Only add scenes that carry a distinct idea, never pad to hit a number.
- Scene 1 (Hook, ~45 sec): Opens with why this matters RIGHT NOW to the learner. A consequence, a scenario, or a provocative question. No definitions, no "in this module you will learn".
- Scene 2–N (Concept, ~40-80 sec each): The essential core only, one idea per scene, shown through a specific example relevant to the learner's job. Max 80 words of prose per scene. **Every Concept scene is immediately followed by its own Practice scene on that exact idea** — never two Concept scenes in a row, and never a Concept scene with no paired Practice anywhere. If a concept doesn't earn a practice moment, it doesn't earn a scene either: fold it into another scene's example or cut it.
- Practice scene(s) (~60 sec each): A decision or recall moment, not a memory test, but something that requires applying the concept just shown. Include 2 sentences explaining why the correct answer is correct.
- Resurface at least one earlier concept in a later scene or practice question, in a new example — not the same question restated. Don't let ideas appear exactly once and never again.
- Last (Summary + action, ~30 sec): 2 bullets: what was learned + one specific thing to do or notice next time. No new information.

For each scene output:
- Scene title (5 words max)
- Core content (max 80 words)
- Visual or layout suggestion
```

Show the generated structure to the user and calibrate before scripting.

**Spaced review:** a single session rarely survives contact with a busy week. If the platform/tooling supports it, recommend a short (1-2 question) review touchpoint 3-7 days after the initial session, re-testing the same behavior with a new example rather than the same question. If the platform can't schedule that automatically, at minimum tell the user this is a known gap so they can plan around it manually.

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
Generate a scene structure: Hook → Concept(s) → Practice(s) → Summary. Scale scene count to the content: one idea per scene, more thin scenes over few dense ones. Every Concept scene is immediately followed by its own Practice scene on that idea, never two Concept scenes in a row. Resurface at least one earlier concept later in the flow instead of covering everything exactly once.
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

--- Documentation-in-disguise test (apply before finalizing) ---
Read this microlearning content. If 3 or more scenes in a row describe the same single tool, feature, or process from different angles (what it is, how to configure it, its limits, its history), answer: is there a concrete "given this situation, what do you do" decision these scenes are building toward, or is this just a manual cut into short scenes? If you can't name the decision, this content belongs in reference documentation, not a microlearning session — cut it down to the one decision that actually matters and drop the rest.
```
