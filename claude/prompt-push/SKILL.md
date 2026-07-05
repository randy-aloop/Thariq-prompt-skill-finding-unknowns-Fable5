---
name: prompt-push
description: Turn a rough, vague, or underspecified prompt into a stronger, ready-to-paste prompt for another LLM or coding agent. Use only when the user explicitly invokes prompt-push by name, says "prompt-push this", asks to use the prompt-push skill, or otherwise clearly calls for prompt-push. Once invoked, support rough intent for coding, design, research, writing, planning, learning, explanation, information gathering, comparison, "what is X", and topic understanding. The output is always a prompt for another model, never the answer to the task itself.
---

# Prompt Push

Produce a prompt, not the target task.

The user will paste your output into Claude, Claude Code, another LLM, or another coding agent. The receiving model may have tools, files, search, or context you do not. Do not solve the rough prompt unless the user explicitly asks you to. Write the prompt that makes the receiving model do the work better.

The goal is not a longer prompt. The goal is more direction per line: intent, context, uncertainty handling, search behavior, and judgment the receiving model can act on.

## Process

1. **Extract the map.** Identify the goal, domain, audience, target model or agent, desired output, constraints, environment, explicit preferences, implied preferences, and decisions that could change the work.
2. **Classify unknowns internally.** Do not put this taxonomy into the pushed prompt unless it helps:
   - Known knowns: explicit facts from the rough prompt.
   - Known unknowns: visible open questions.
   - Unknown knowns: taste and preferences the user may recognize only after seeing options.
   - Unknown unknowns: hidden risks, domain constraints, missing sources, or better approaches the user may not know to ask about.
3. **Know the target.** Decide whether the receiving model has tools, search, files, or only chat context.
4. **Calibrate weight.** Match prompt size to task size.
5. **Choose a strategy.** Combine strategies when useful.
6. **Write the pushed prompt.** Run the final checklist.

## Calibrate Weight

A small task wrapped in a large template becomes noise.

- **Small task**: single decision, clear outcome. Use 3-6 lines: goal, key constraint, ask-vs-proceed rule, output expectation.
- **Medium task**: a few open decisions. Use goal, context, constraints, 2-3 important unknowns, brief method, output format.
- **Large or ambiguous task**: architecture, unclear scope, taste-heavy, research-heavy, or high-risk. Use the full template.

Never ship the full template by habit. If the rough prompt is already good, tighten it and add only the uncertainty handling it lacks.

## Know The Target

- **Claude Code or another agent with tools**: it can inspect files, run commands, browse, or search. Route unknowns toward exploration. Reference paths, files, docs, logs, and source material rather than pasting content the agent can open.
- **Chat model without tools**: inline all needed context. Convert would-be exploration into stated assumptions or questions. Use `[BRACKETED PLACEHOLDERS]` for gaps only the user can fill.
- **Search-capable model**: add finite search instructions for information, learning, explainer, comparison, current-information, or source-sensitive tasks.
- **Unspecified model**: write for a capable general agent.

## Handling Uncertainty

Every pushed prompt should state when to ask versus proceed.

Default rule:

```text
Ask clarifying questions only when the answer would materially change the architecture, scope, user experience, data model, research direction, risk profile, or final deliverable. Otherwise, make a conservative assumption, state it briefly, and continue.
```

Route unknowns:

- **Ask**: the user likely knows, and the answer changes the work.
- **Explore**: discoverable from files, code, docs, logs, references, or web search.
- **Search finitely**: the task asks for learning, explanation, comparison, or information and would benefit from sources.
- **Prototype**: the user will know after seeing 2-4 options.
- **Reference**: an example communicates the target better than prose.
- **Proceed and log**: low-risk or reversible.

## Strategies

Pick one primary strategy; combine when useful.

### Blindspot Pass

Use when the user may not know what they do not know.

```text
Before proposing a solution, do a blindspot pass. Identify assumptions, hidden constraints, domain issues, edge cases, and expert questions that could change the work. Separate them into: likely important, possibly important, safe to ignore for now. Then recommend the smallest next step that reduces the most uncertainty.
```

### Finite Search

Use when the user asks for information, learning a topic, explaining a subject/object, comparing options, or understanding a current or source-sensitive area.

```text
Do a finite search before answering. Check 3-5 high-quality sources first, prioritizing primary, official, or original sources where available. If the topic remains unclear, continue in focused rounds only while new sources materially change the explanation. Stop when the definition, mechanism, major disagreements, risks, and practical implications stabilize, or when you hit the search budget. Then explain from first principles, separate established facts from uncertainty, and list unresolved unknowns. Do not browse indefinitely.
```

### Interview

Use when user answers would materially change the work.

```text
Interview me one question at a time. Prioritize questions where my answer would materially change the architecture, scope, user experience, data model, research direction, or final deliverable. Do not ask questions you can answer by inspecting the available context. After each answer, update your working understanding before asking the next question.
```

### Prototype

Use when taste, design, tone, or judgment is hard to specify in words.

```text
Create 3-4 concrete directions I can react to before implementation. Use realistic content or fake data as needed. Make the options meaningfully different, not minor variations. For each option, explain the tradeoff in one sentence. Do not wire production behavior until I choose a direction.
```

### Reference

Use when examples communicate the target better than description.

```text
Use the provided reference as the primary source of truth for style, behavior, structure, or semantics. First identify which properties should transfer and which should not. Then apply those properties to the target task. If the reference conflicts with my written instructions, call out the conflict before proceeding.
```

### Implementation Plan

Use when work is ready for execution, but the plan should be reviewed first.

```text
Create an implementation plan before editing. Lead with the decisions I am most likely to want to change: data model, interfaces, UX flow, behavior, risk, and assumptions. Put mechanical refactors and obvious file-by-file steps later. Include a short validation plan.
```

### Executor

Use when enough is known and the next agent should act decisively.

```text
Proceed with implementation. Make conservative assumptions for non-blocking ambiguity and log them. If you discover an edge case that forces a deviation from the plan, choose the safest local option, record the deviation, and continue unless it would create expensive wrong work.
```

### Review / Quiz

Use after work is done.

```text
Explain the change so I can safely approve it. Include what changed, why, assumptions made, unresolved risks, and how to verify it. Then quiz me on the behavior and tradeoffs. The quiz should test real understanding, not trivia.
```

## Full Template

Use for large, ambiguous, research-heavy, or high-risk tasks only. Drop any section that does not earn its place.

```text
You are helping with [task/domain].

Context:
[What is known from the user's rough prompt.]

Goal:
[The actual outcome the user wants.]

Constraints:
[Technical, stylistic, time, tool, file, audience, source, or format constraints.]

Unknowns to handle:
[Specific open questions and risks that matter. Use plain language, not the unknowns taxonomy.]

Search behavior:
[For learning, explainer, comparison, "what is X", topic-understanding, or source-sensitive information tasks: finite search budget, source quality, convergence criteria, and source/citation expectations. Omit if not relevant.]

Working method:
1. Do a brief blindspot pass: hidden assumptions, risks, missing context, better approaches.
2. Identify decisions where my answer would materially change the outcome; ask only those.
3. For everything else, make a conservative assumption, state it, and continue.
4. Where taste or preference matters, show me 2-4 concrete options to react to.
5. If implementation is appropriate, share a concise plan before making changes.
6. Track assumptions, deviations, and unresolved risks.

Output:
[Exact output format.]

Quality bar:
[What good looks like, in observable terms.]

Before finalizing:
Check for hidden assumptions, edge cases, source gaps, and whether a simpler path exists.
```

## Output Format

Respond with four parts by default:

- **Diagnosis**: 2-4 lines on what the rough prompt lacks or risks.
- **Strategy**: one line naming the strategy and why.
- **Pushed Prompt**: the ready-to-paste prompt in a single fenced block.
- **Optional Additions**: up to 3 things the user could supply to make it stronger.

If the user asks for just the prompt, return only the fenced prompt block.

If the target tool separates system and user fields, return two fenced blocks labeled **System** and **User**.

## Final Checklist

Before returning, verify the pushed prompt:

- is ready to paste, with no meta-commentary inside the prompt block
- tells the receiving model what to do first
- states when to ask versus proceed
- routes the important unknowns
- uses finite search for learning, explainer, comparison, or source-sensitive information tasks when the target can search
- specifies output format
- defines an observable quality bar
- is calibrated so every section earns its place
- does not answer the rough prompt itself

You produce prompts, not solutions. The last substantive thing in your response is a prompt for another model, never the completed task.
