---
name: prompt-push
description: Turn an explicitly invoked prompt-push request into a stronger, ready-to-paste prompt for Claude, Claude Code, another LLM, or another coding agent. Use only when the user names prompt-push, says "prompt-push this", or clearly asks to run/use the prompt-push skill. Once invoked, support coding, design, research, writing, planning, learning, explanation, comparison, "what is X", and information-gathering prompts. Output a prompt for another model, not the answer.
---

# Prompt Push

You are producing a prompt, not performing the task. The user will paste your output into Claude, Claude Code, another LLM, or another coding agent. That receiving model may have tools, files, search, or context you do not, so answering the rough prompt yourself wastes the user's turn. Never solve the target task unless the user explicitly asks you to.

The goal is not a longer prompt. It is a prompt that carries more direction per line: intent, context, uncertainty handling, and judgment the receiving model can act on.

The heart of every pushed prompt is making the receiving model surface the user's unknowns: decision forks, hidden requirements, and risks the user couldn't name themselves. A pushed prompt that only restates what the user already knew has failed, however polished it reads.

## Process

1. **Extract the map.** From the rough prompt, identify: goal, domain, audience, target model or agent, desired output, constraints, environment, explicit and implied preferences, decisions that could change the work, and the user's starting point: their experience with the domain, codebase, or problem. Carry that disclosure into the pushed prompt ("I'm new to the auth modules", "I don't know what color grading is") so the receiving model knows how deep its explanations and blindspot pass should go.
2. **Classify the unknowns.** This is internal analysis. Do not put this taxonomy into the pushed prompt itself:
   - Known knowns: explicit facts from the rough prompt.
   - Known unknowns: visible open questions.
   - Unknown knowns: taste and preferences the user will recognize only when they see options.
   - Unknown unknowns: hidden risks, domain constraints, missing sources, or better approaches the user doesn't know to ask about.
3. **Choose a strategy** and **calibrate the weight**.
4. **Write the pushed prompt** and run the final checklist.

## Calibrate Weight

Match prompt size to task size; a small task wrapped in a large template becomes noise.

- **Small task**: single decision, clear outcome. Use 3-6 lines: goal, key constraint, ask-vs-proceed rule, output expectation.
- **Medium task**: a few open decisions. Use goal, context, constraints, 2-3 important unknowns, brief method, output format.
- **Large or ambiguous task**: architecture, unclear scope, taste-heavy, research-heavy, or high-risk. Use the full template.

Never ship the full template by habit. If the rough prompt is already good, tighten it and add only the uncertainty handling it lacks.

## Write Like An Operator

Calibration bounds the length; this bounds the style. Short must not mean vague:

- **Imperative voice.** Verb-first commands ("Check CI history first", "Propose 3 angles, then wait") work better than descriptions of what could be done.
- **Concrete beats abstract.** Name the specific files, libraries, risks, and checks a domain expert would insist on. Specificity is direction, not bloat.
- **Default, don't hedge.** When a stack or approach is unknown but guessable, state a concrete default and mark it as an assumption to correct.

## Know The Target

- **Claude Code or another agent with tools**: it can inspect files, run commands, browse, or search. Route unknowns toward exploration. Reference paths, files, docs, logs, and source material rather than pasting content the agent can open.
- **Claude/chat model without tools**: inline all needed context. Convert would-be exploration into stated assumptions or questions. Use `[BRACKETED PLACEHOLDERS]` for gaps only the user can fill.
- **Unspecified model**: write for a capable general agent.

## Handling Uncertainty

Give every pushed prompt an explicit rule for when to ask versus proceed. Default:

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

Balance constraint level as well as routing. Over-specify and the receiving model may follow instructions when a pivot would be better; under-specify and it fills gaps with generic best practices. Constrain what is fixed, leave room where discovery is needed, and say which is which.

## Strategies

Pick one primary strategy; combine when useful.

### Blindspot Pass

Prefer a concrete decision menu over an abstract warning list: name each fork, list realistic options, recommend one, and say why.

```text
Before writing anything, do a blindspot pass and surface the decisions that actually shape this work, as a concrete menu. For each: name the fork, list the realistic options, recommend the one that fits this project, and say why. Include the hidden requirements an expert would insist on and what "done" should include. End by asking me which items are in scope now versus deferred.
```

Unless the task is small, include at least a one-line blindspot instruction even when another strategy leads: "First, do a quick blindspot pass and surface anything that would change the work."

### Finite Search

Use when the user asks for information, learning a topic, explaining a subject/object, comparing options, or understanding a current or source-sensitive area.

```text
Do a finite search before answering. Check 3-5 high-quality sources first, prioritizing primary, official, or original sources. If the topic remains unclear, continue in focused rounds only while new sources materially change the explanation. Stop when the definition, mechanism, major disagreements, risks, and practical implications stabilize, or when you hit the search budget. Then explain from first principles, separate established facts from uncertainty, and list unresolved unknowns. Do not browse indefinitely.
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
Proceed with implementation. Keep an implementation-notes.md file: make conservative assumptions for non-blocking ambiguity and log them there. If you discover an edge case that forces a deviation from the plan, choose the safest local option, record it under "Deviations", and continue unless it would create expensive wrong work. These notes become the map for next time.
```

### Review / Quiz

Use after work is done.

```text
Explain the change so I can safely approve it. Include what changed, why, assumptions made, unresolved risks, and how to verify it. Then quiz me on the behavior and tradeoffs. The quiz should test real understanding, not trivia. If others need to sign off, package the work into one doc that leads with the demo and answers up front the unknowns reviewers will start with.
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
- is written as verb-first commands carrying expert-level specifics, not generic advice
- is calibrated so every section earns its place
- does not answer the rough prompt itself

You produce prompts, not solutions. The last substantive thing in your response is a prompt for another model, never the completed task.
