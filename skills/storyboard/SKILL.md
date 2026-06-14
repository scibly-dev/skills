---
name: storyboard
description: Turns a course outline, scene plan, or module structure into a multimedia production plan — deciding per scene whether it needs an infographic, animation, video, interactive scenario, quiz, or plain text, with the didactic reasoning and a ready-to-use brief for your production tooling (e.g. an infographic, animation, or video-production skill). Use this whenever a course structure or scene plan already exists and the next question is "what do we actually produce, and in what order?" Trigger on phrases like "where does an animation/infographic make sense", "media plan", "storyboard", "what should we visualize", "production plan", or whenever someone is about to start producing visuals/videos for a course and hasn't yet decided which scenes get which treatment. Also trigger if someone asks to prioritize multimedia production for a course that's otherwise scoped.
---

# Storyboard

A course outline tells you *what* to teach. A storyboard tells you *how to show it* — and, just as importantly, where showing anything beyond text would be wasted effort.

This skill sits between planning (`needs-analysis`, `didactic-reduction`, `scenario-generation`) and production (an infographic, animation, or video-production skill). Its job is to turn a scene list into a prioritized, justified media plan that production skills can pick up directly.

## Step 1 — Gather inputs

You need:

1. **The course/lesson structure** — a scene plan, module plan, or scene list. If the user hasn't provided one, ask for it or look for `*scene-plan*.md`, `*storyboard*.md`, or similar files in the project folder.
2. **Production capacity** — which production tools are realistically available (an infographic skill, an animation skill, a video tool like Remotion, screen recording, stock video)? If unclear, assume infographic and animation (HTML/CSS or SVG-based) production are available, since those need no extra setup.
3. **Constraints** (ask only if not obvious) — total course duration, deadline, whether this is a pilot/MVP or a polished release.

Don't over-interview. If the scene list already has format hints (e.g. "Format: side-by-side comparison"), treat those as a starting point, not gospel — your job is to sanity-check and fill gaps, not redo everything from scratch.

## Step 2 — Classify each scene by pedagogical purpose

For every scene, ask: **what is this scene doing for the learner?** The purpose — not the topic — determines the medium.

| Purpose | How to recognize it | Medium that fits best |
|---|---|---|
| Hook / emotional entry | First scene of a module; "why should I care" | Animation (narrative, before/after) |
| Concept / mental model | "What is X", a metaphor, a taxonomy, "how the pieces relate" | Infographic |
| Process / sequence | "How to do X step by step"; order and timing matter | Animation (progressive reveal) |
| Demonstration | A tool, an interface, a real workflow in motion | Screen-recorded video |
| Decision / scenario | "What would you do if...", judgment calls, risk, ethics | Interactive scenario / branching / multiple choice |
| Practice / retrieval | Checking or applying what was just learned | Multiple choice, short exercise |
| Summary / synthesis | End of module, "what to remember" | Infographic (recap) or plain text |

Most scenes are **plain rich text/HTML** — that's correct and good. A storyboard that recommends visuals for everything is a bad storyboard. The interesting work is identifying the handful of scenes where a visual genuinely changes comprehension or motivation, and being honest about the rest.

## Step 3 — Decide medium vs. plain text

For each scene that *isn't* obviously a hook, concept, or process, run it through this check before reaching for production:

**Lean toward a visual (infographic/animation/video) when:**
- This is the learner's *first* encounter with a concept that has 3+ interrelated parts
- The content describes change over time, a sequence, or cause-and-effect
- The scene needs to create an emotional reaction (frustration, relief, urgency) to land
- Learners will want to refer back to this as a reference later (favors infographic)

**Lean toward plain text/rich HTML when:**
- The scene explains steps the learner will follow *while doing the task* (they need to read at their own pace, not watch)
- It's a transition, instruction, or orientation scene
- The content is already well-served by a list, table, or code block
- Production effort would be disproportionate to the learning gain

**Within visuals, choose:**
- **Infographic** for structure, comparison, relationships, "the big picture" — things that hold still
- **Animation** for sequence, transformation, before/after, "things that change" — reference your project's animation style guide, if you have one, for narrative vs. layer-reveal style
- **Video** only when a real interface, person, or physical demonstration is the point — animations are usually cheaper and more controllable for everything else

If the course has a guiding metaphor (in German L&D this concept is called the *Leitmetapher*), check whether the proposed visual reinforces it. A visual that contradicts or ignores the course's central metaphor is worse than no visual.

## Step 4 — Produce the storyboard

Output two parts: an overview table, then detailed briefs for the highest-priority items.

### Part A — Overview table

```markdown
| # | Scene | Medium | Rationale (1 sentence) | Production | Priority |
|---|-------|--------|-------------------------|-------------|----------|
| 1.1 | The Monday-Morning Problem | Animation | Emotional hook needs movement & contrast | animation | 🔴 Must |
| 1.2 | The Three-Box Model | Infographic | Structural knowledge, 3 elements + relationships | infographic | 🔴 Must |
| 1.3 | Skill vs. Knowledge | Text + comparison table | A comparison table is enough, no added value from an image | — | — |
```

Priority scale:
- 🔴 **Must** — a core learning moment; without this medium the learning goal is at risk
- 🟡 **Should** — meaningfully improves comprehension or engagement, but text would still work
- 🟢 **Could** — nice polish if time/budget allows
- *(blank/—)* — plain text is the right call; don't manufacture a visual for it

Be honest about the ratio. A 15-scene course with 2–3 🔴 items and most scenes marked "—" is healthy. If everything comes out 🔴, re-apply Step 3 more critically.

### Part B — Production briefs

For every 🔴 (and 🟡 if capacity allows), write a brief in this format:

```markdown
### [Scene ID] — [Title]
**Medium:** Animation | Infographic | Video | Interactive
**Production tool:** animation skill | infographic skill | video tool (e.g. Remotion)
**Estimated length:** ~Xs (animation/video) or — (infographic)

**What's shown:**
[Concrete description of what appears, in what order — enough that an
animation or infographic skill could be invoked with this brief directly as input]

**Key message:**
[The single takeaway — what should stick after this plays]

**Didactic rationale:**
[Why this medium and not text — tie back to Step 3's reasoning]
```

Write these so they can be pasted straight into your animation or infographic production skill as the input brief.

## Step 5 — Recommend production order

Close with a short, ordered punch list:

1. Start with 🔴 items that establish the course's guiding metaphor or recurring visual language — everything else should look consistent with these
2. Then 🔴 hooks (first scene of each module) — they have outsized impact on completion
3. Then remaining 🔴 items
4. 🟡 items only if the above is done and there's remaining time/budget

If the project has an existing visual style (check for a design-system file, prior infographics/animations in the output folder), note that new productions should match it — don't restate the whole design system, just point to it.

## A note on restraint

The temptation with this skill is to find a reason to visualize everything — visuals are fun to plan and fun to build. Resist it. The scenes marked "—" are not failures of imagination; they're scenes where the learner is better served by reading at their own pace. A storyboard's value comes from *prioritization*, not from maximizing the visual count. If you're unsure whether something deserves a 🔴 or a 🟡, it's probably a 🟡 — or a "—".
