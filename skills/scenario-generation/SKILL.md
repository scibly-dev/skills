---
name: scenario-generation
description: Scenario & Case Study Generation with AI for L&D teams. Use this skill when an instructional designer, L&D professional, or course developer wants to create realistic workplace scenarios, branching cases, role-play situations, or decision-based learning moments using AI. Trigger when someone needs more scenarios than they can write manually, wants to speed up scenario development, is stuck making situations feel realistic, or says things like "I need practice scenarios", "write me some case studies", "I need branching situations", or "can you generate some realistic examples for this topic".
---

# AI Scenario Generation

Scenarios are the highest-impact learning activity — and the hardest to write well. With the right brief, AI can generate 10 realistic workplace scenarios in the time it used to take to write one.

## Step 1 — Understand what you need

Ask the user:

1. **What job role is the learner in?** (e.g. "customer service rep", "project manager", "new nurse")
2. **What skill or decision is the scenario testing?** (e.g. "handling an angry customer without escalating", "prioritizing tasks under pressure", "recognizing signs of medication error risk")
3. **What type do you need?**
   - **Linear**: situation → action → consequence (good for reflection and case study discussion)
   - **Branching**: multiple decision points with paths and consequences (good for practice and LMS delivery)
   - **Role-play brief**: a character + situation brief for facilitator-led practice
4. **How many?** (1 detailed, or a set of 3–10 shorter variations?)
5. **What should vary across the set?** Difficulty, context, customer type, stakes, industry — variety makes practice richer.

## Step 2 — Build the scenario generation prompt

Construct this prompt with the user's inputs:

```
SCENARIO GENERATION PROMPT

Role: You are an instructional designer and scenario writer.
Learner: [job role + experience level]
Skill being practiced: [specific skill or decision]
Scenario type: [linear / branching / role-play brief]
Number of scenarios: [N]
Variation axis: [what should differ — difficulty, context, character type, stakes]

For each scenario, include:
- Setup: 2–3 sentences describing the situation. Make it specific and realistic — real details, real pressures, real people.
- The moment: the exact point where the learner must decide or act.
- [If branching] Options: 3 choices — one clearly right, one plausible but wrong, one tempting shortcut with a hidden cost.
- [If branching] Consequences: what happens next for each choice, including realistic downstream effects.
- [If linear] Reflection prompt: one question asking what the learner would do and why.

Tone: [professional / empathetic / urgent / neutral].
Avoid: [jargon / overly dramatic situations / unrealistic dialogue].
```

Show the built prompt. Run it if the user wants to see output now.

## Step 3 — Review and calibrate

After generating scenarios, help the user assess quality:

- **Realistic?** Would this actually happen in that job? Could a learner recognize themselves in it?
- **Ambiguous enough?** The wrong choices should be genuinely tempting, not obviously bad.
- **Specific enough?** Vague situations ("a difficult situation with a client") are far less effective than specific ones ("a client emails Friday at 4pm saying the deliverable you promised Monday isn't good enough").
- **Varied?** If generating a set, do the scenarios cover different contexts, stakes, and character types?

Offer to regenerate with calibration if needed.

## Step 4 — The scenario prompt blueprint to take away

```
SCENARIO PROMPT BLUEPRINT

Role: You are an instructional designer and scenario writer.
Learner: [job role + experience level]
Skill: [specific skill or decision point]
Type: [linear / branching / role-play brief]
Count: [N]
Vary by: [difficulty / context / character / stakes]

Per scenario:
- Setup (2–3 specific, realistic sentences)
- The decision moment
- [Branching] 3 options + realistic consequences for each
- [Linear] 1 reflection question

Tone: [tone]. Avoid: [exclusions].

CALIBRATION TIPS:
- If scenarios feel generic: add "Include specific job-relevant details like [tool names, process steps, client types]"
- If wrong choices are too obvious: add "Wrong options should reflect genuine misconceptions or common shortcuts, not obvious errors"
- If stakes feel low: add "The outcome of the wrong choice should have a realistic professional consequence"
```
