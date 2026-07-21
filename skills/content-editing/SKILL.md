---
name: content-editing
description: Content Editing with AI for L&D — the First-Draft Machine approach. Use this skill when an instructional designer or L&D professional has AI-generated content they want to improve, or wants to learn how to use AI as a first-draft tool and edit the output effectively. Trigger when someone pastes AI output and asks "is this good?", wants to speed up content development while maintaining quality, asks what to look for when reviewing AI-generated learning content, or says things like "this feels off but I can't explain why" or "the AI keeps writing in a weird tone".
---

# AI Content Editing: The First-Draft Machine

AI writes the draft. You make it good. The loop is: generate → review → refine. This skill walks you through what to look for and how to fix it fast — cutting development time without cutting quality.

## Step 1 — What are we working with?

Ask the user:

1. **Paste the AI-generated content** (or describe it if it's long).
2. **What type of content is this?** (scene script, learning objective, facilitator guide, email, job aid, course description, scenario, ...)
3. **Who is the audience?** (role, experience level, context)
4. **What was the original prompt used?** (optional, but useful for spotting structural problems vs. prompt problems)

## Step 2 — Review the content

Analyze the pasted content against these five fault lines. For each issue found, quote the specific text and give a concrete fix.

### Fault Line 1: Tone mismatch
AI defaults to formal, corporate-sounding language. Check: does this sound like how the target audience actually talks at work? Flag jargon, passive voice, and HR-speak.

*Red flags*: "It is important to note that...", "Employees should ensure that...", "In order to facilitate...", any sentence longer than 25 words, em/en dashes used as a sentence connector (a well-known AI tell; replace with a period, comma, or rewrite the sentence).

### Fault Line 2: Accuracy gaps
AI generates plausible-sounding content that may be wrong. Check: are there specific claims, statistics, regulatory details, or technical steps that need human verification? Flag anything that "sounds made up" or unusually specific without a source.

### Fault Line 3: Wrong depth
AI pitches content at the wrong level — over-explaining basics to experts, or skipping essential context for beginners. Check: is the depth right for the stated audience? Would an expert skip it out of boredom? Would a novice be lost?

### Fault Line 4: Missing learner relevance
AI explains the *what* but skips the *so what*. Check: does the content tell the learner why this matters to *them* specifically? Is there a "you" in the content?

### Fault Line 5: Structural problems
AI sometimes buries the main point, over-uses bullets, or creates walls of text. Check: is the main point visible within the first two sentences? Does structure serve the reader or just make the content look complete? On mobile-first or short-form content, also check for heading-overuse (H1/H2/H3 stacked every paragraph): bold key phrases inline instead of a new heading for every sub-point; it reads better on a small screen and doesn't fragment short content into artificial sections.

## Step 3 — Produce the edited version

After flagging issues, produce a revised version:
- Fix tone inline
- Mark accuracy claims with `[VERIFY: ...]`
- Adjust depth by adding or removing explanation based on audience
- Add a "why this matters to you" sentence where it's missing
- Restructure if the main point is buried

Then briefly explain the 2–3 most important changes you made, so the user can apply the same thinking next time.

## Step 4 — Improve the prompt if the draft was poor

If the AI output was structurally wrong (not just tonally off), suggest improving the upstream prompt. Ask: "Do you want me to build a better prompt for this content type so the next draft needs less editing?" Then use the instructional-prompt-engineering skill.

## Step 5 — The L&D editor's checklist to take away

```
L&D EDITOR'S CHECKLIST FOR AI CONTENT

TONE
□ Does it sound like a human, not a corporate document?
□ No passive voice throughout?
□ No "it is important to note that" or similar throat-clearing?

ACCURACY
□ All statistics and data points verified against primary sources?
□ All citations confirmed to exist?
□ Compliance/regulatory content reviewed by an SME?

DEPTH
□ Pitched at the right level for this specific audience?
□ No definitions the audience already knows?
□ No jargon without explanation for a novice audience?

RELEVANCE
□ Does the learner know why this matters to them?
□ Is there a "you" in the content — is it addressed to the learner?
□ Are examples specific to the learner's actual job context?

STRUCTURE
□ Main point visible within the first 2 sentences?
□ No bullet list with more than 5 items?
□ No paragraph longer than 4 sentences?
□ Short-form/mobile content: bold for emphasis instead of a new heading per sub-point?

RED FLAGS (fix immediately)
□ "In conclusion..." → cut
□ "In order to..." → replace with "To"
□ Any stat without a source → verify or remove
□ Definitions the audience already knows → cut
□ Em/en dash used as a connector → replace (common AI tell)
```
