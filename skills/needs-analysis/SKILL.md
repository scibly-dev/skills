---
name: needs-analysis
description: AI-Assisted Needs Analysis for L&D professionals. Use this skill when an instructional designer, L&D professional, or trainer wants to analyze training needs, identify skill gaps, synthesize SME input, process transcripts or job descriptions into a learning brief, map competencies, or figure out what a course or program should cover. Trigger whenever someone wants to figure out WHAT to teach before building anything — even if they just say "I have a bunch of SME notes", "where do I start", "what should this course cover", or "help me scope this training".
---

# AI-Assisted Needs Analysis

You're about to cut your training needs research from days to hours. This skill walks you through using AI to synthesize raw inputs — SME transcripts, job descriptions, performance data, stakeholder interviews — into a structured skill gap map you can actually build from.

## Step 1 — Understand the situation

Ask the user these questions. If some are already clear from context, fill them in and ask only what's missing.

1. **What's the training area or performance problem?** (e.g. "new managers aren't having effective 1:1s", "sales reps miss quota on enterprise deals")
2. **Who is the target learner?** (role, experience level, context)
3. **What raw material do you have?** Choose from:
   - SME interview transcripts or notes
   - Job descriptions / competency frameworks
   - Performance review data or feedback themes
   - Stakeholder interviews or complaints
   - Existing course content to update
   - Nothing yet (we'll build a synthesis prompt to gather it)
4. **What's the output used for?** (a course proposal, a learning brief, a curriculum map, a conversation with a stakeholder)

Once you have answers, proceed.

## Step 2 — Synthesize the inputs

### If the user has raw material to paste

Ask them to paste it. Then synthesize using this structure:

```
SKILL GAP ANALYSIS: [Topic]

Target Learner: [role + context]

Key Gaps Identified:
1. [Gap] — Evidence: [quote or data point]
2. [Gap] — Evidence: [quote or data point]
3. [Gap] — Evidence: [quote or data point]

Root Cause Hypothesis:
[2–3 sentences: is this a knowledge gap, a skill gap, or a motivation/environment issue?]

What Learning Can Address:
[Be honest — what training can fix vs. what is a process/system/management issue]

Priority Focus for a Course/Program:
[The 1–2 gaps that training should target first, and why]

What to Exclude:
[Gaps better solved by process change, coaching, or hiring — not training]
```

### If the user has no material yet

Give them this prompt to use with an AI or to run with a stakeholder:

```
NEEDS ANALYSIS INTERVIEW PROMPT

You are helping me conduct a training needs analysis. Ask me questions one at a time to uncover:
1. The specific performance gap or problem (not the assumed solution)
2. Who is affected and in what context
3. What good performance looks like vs. current state
4. What's getting in the way (knowledge, skill, motivation, tools, process)
5. What has been tried before and what happened

After gathering answers, produce a structured skill gap summary with:
- Key gaps identified
- Root cause hypothesis
- What learning can address
- What learning cannot address
- Recommended focus areas

Start with: "Tell me about the performance problem or training need you're investigating."
```

## Step 3 — Produce the gap map

Once you have the synthesis, format it clearly and offer:
- A **learning objective draft** for the top priority gap
- A **recommended scope statement** (what the course/program will and will NOT cover)
- A **stakeholder summary** (2–3 sentences suitable for a proposal or slide)

## Step 4 — Prompt template to take away

Always end with this reusable prompt the user can save:

```
NEEDS ANALYSIS SYNTHESIS PROMPT

Role: You are an expert instructional designer.
Task: Analyze the following [transcripts / job descriptions / feedback data] and identify skill gaps.
Audience: [Target learner role and context]

Output format:
- Top 3–5 skill gaps with supporting evidence
- Root cause for each gap (knowledge vs. skill vs. motivation vs. environment)
- What training can realistically address
- What training cannot address (process/system/management issues)
- Recommended learning focus: the 1–2 gaps with highest training ROI

[PASTE YOUR RAW MATERIAL HERE]
```

Tell the user to replace the bracketed fields with their specifics before using it.
