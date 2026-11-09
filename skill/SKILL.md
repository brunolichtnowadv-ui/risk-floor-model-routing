---
name: model-and-effort-routing
description: Use as the first action of any task with more than one or two actions, and again before each step: split the task into steps, recommend a model and an effort level for each one (with a risk floor for deadlines, amounts, citations and irreversible actions), ask the user before switching the main conversation, and set subagent model and effort directly.
---

# Model and effort routing

Goal: spend the least that still gives the same result, and never cut corners where a mistake is expensive.

## 1. Who changes what

- **Main conversation model and effort:** only the user, in the app's model picker and effort setting. The skill recommends and asks; it never claims to have switched anything.
- **Subagent model and effort:** the skill decides, through the subagent's `model` and `effort` parameters, without asking.

## 2. Model profiles

- **Haiku:** fastest, lowest cost. Extraction, classification, simple summaries, file handling.
- **Sonnet:** everyday work. Writing, analysis and multi-step tasks with good speed.
- **Opus:** deep reasoning and complex multi-step tasks.
- **Fable:** most capable, slowest and most expensive. Largest and most critical work.

Rule of thumb: follows a template or a known route, Sonnet. Needs an argument or a decision, Opus. Decisive, Fable. Nothing involving a risk-floor category (section 4) stays on Haiku without a check.

## 3. Activity map (edit to match your work)

| Activity | Model | Effort |
|---|---|---|
| Organize, rename and move files; simple searches in folders | Haiku | low |
| Mechanical extraction into lists and tables | Haiku | low |
| Read long documents and summarize them (read-only subagent) | Sonnet | medium |
| Check sources, references and quotations against the original | Sonnet | medium |
| Draft from an existing template; batch work from a known pattern | Sonnet | medium |
| Analysis that needs an argument or decision; complex calculations | Opus | high |
| Create or revise skills and style guides; final review before submitting | Opus | high |
| High-stakes or novel work; long autonomous tasks where quality outweighs time | Fable | high (max only by exception) |

## 4. Risk floor

Risk-floor categories (edit to match your field): **deadlines, amounts, citations and quotations, and any irreversible action** (submit, sign, send, delete).

- A step that touches a risk-floor category never runs below `medium` effort.
- Its output is checked in the main conversation before it is used. A Haiku or Sonnet result in these categories is never passed on unchecked.
- Doubtful result from a `low` or `medium` step: redo only the doubtful point one tier up, not the whole step.
- Outside these categories, choose the lowest effort that gives the same result. Do mechanical checks with targeted searches, not by re-reading everything.

## 5. Pre-flight (start of task and before each step)

0. **Order:** this skill is the first action of the task, before any other skill or tool. If a tool was already called for this task without the pre-flight, stop, say so in one line, and run it now.
1. Classify the task with the map and split it into steps when it mixes natures.
2. Compare with the current model of the conversation (the session's model ID).
3. Ask the user, with the recommendation first and marked as recommended, in the format `Model: X | Effort: Y`, with the reason in a few words. Offer the other models and "keep current" as options. Steps of the same nature and the same model can share one question.
4. Wait for the answer. If the user picks a model or effort different from the current one, stop and show, highlighted: "Switch to X (effort Y) in the model picker, then reply 'go'." Selecting the option does not switch anything, and a switch in the picker only takes effect from the user's next message, so that one-word reply is needed. On that message, check the session's model ID; if the switch did not happen, repeat the warning once. If the user keeps the current model, proceed.
5. **Unattended or scheduled runs:** state the recommendation in one line and proceed.
6. **Simple tasks (one or two actions):** no pre-flight.

## 6. Running by steps

- Subagents get closed steps: what to read, where, and what to return. Their result comes back summarized.
- The main conversation keeps: final analysis, final writing, the dialogue with the user, visible actions in the user's apps, and every irreversible action.
- Example: read the source documents (Sonnet, medium) > find the right template (Sonnet, medium) > argument and drafting (Opus, high, main conversation) > final check (main conversation) > save and log (main conversation).

## 7. Keeping the map current

Triggers:

- The user corrects a choice ("Sonnet can do this", "this needed Opus").
- A step had to be redone because the model was not enough.
- A cheaper model delivered well, consistently.
- A new activity appears that has no row in the map.
- A model is released or changed.

When a trigger happens, record it in the history below (date, activity, from > to, reason) and, at the end of the task, propose the updated skill to the user in one line, without interrupting the work.

## 8. History

- YYYY-MM-DD: initial map.
