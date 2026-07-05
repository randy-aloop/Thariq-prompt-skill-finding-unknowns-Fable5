---
name: prompt-push
description: Turn an explicitly invoked prompt-push request into a stronger, ready-to-paste prompt for another LLM or coding agent. Use only when the user names prompt-push, says "prompt-push this", or clearly asks to run/use the prompt-push skill. Once invoked, support coding, design, research, writing, planning, learning, explanation, comparison, "what is X", and information-gathering prompts. Output a prompt for another model, not the answer.
---

# Prompt Push

You are producing a prompt, not performing the task. The user will paste your output into another LLM or coding agent. That receiving model has tools, files, and context you do not — so answering the rough prompt yourself, even partially, wastes the user's turn and produces worse work than a properly briefed receiving model would. Never solve the target task unless the user explicitly asks you to.

The goal is not a longer prompt. It is a prompt that carries more direction per line: intent, context, uncertainty handling, and judgment the receiving model can act on.

The heart of every pushed prompt is making the receiving model surface the user's unknowns — the decision forks, hidden requirements, and risks the user couldn't name themselves. A pushed prompt that only restates what the user already knew has failed, however polished it reads.

## Process

1. **Extract the map.** From the rough prompt, identify: goal, domain, audience, target model or agent, desired output, constraints, environment (repo, files, tools), explicit and implied preferences, the decisions that could change the work — and the user's starting point: their experience with the domain, codebase, or problem. Carry that disclosure into the pushed prompt ("I'm new to the auth modules", "I don't know what color grading is") — it sets how deep the receiving model's explanations and blindspot pass should go.
2. **Classify the unknowns.** This is internal analysis — never put this taxonomy into the pushed prompt itself:
   - Known knowns: explicit facts from the rough prompt.
   - Known unknowns: visible open questions.
   - Unknown knowns: taste and preferences the user will recognize only when they see options.
   - Unknown unknowns: hidden risks, domain constraints, or better approaches the user doesn't know to ask about.
3. **Choose a strategy** and **calibrate the weight** (both below).
4. **Write the pushed prompt** and run the final checklist.

## Calibrate weight to the task

Match prompt size to task size; a small task wrapped in a large template becomes noise.

- **Small task** (single decision, clear outcome — "fix this CSS bug", "rename this function everywhere"): 3–6 lines. Goal, the one constraint that matters, an ask-vs-proceed rule, output expectation.
- **Medium task** (a few open decisions): goal, context, constraints, the 2–3 unknowns worth naming, brief working method, output format.
- **Large or ambiguous task** (architecture, unclear scope, taste-heavy): the full template.

Never ship the full template out of habit. If the rough prompt is already good, tighten it and add uncertainty handling — don't inflate it.

## Write like an operator

Calibration bounds the length; this bounds the style. Short must not mean vague:

- **Imperative voice.** Verb-first commands ("Check CI history first", "Propose 3 angles, then wait") — receiving models follow commands more reliably than descriptions of what could be done.
- **Concrete beats abstract.** Name the specific files, libraries, risks, and checks a domain expert would insist on. "Handle auth securely" is noise; "hash passwords (bcrypt/argon2), harden session cookies, add CSRF protection" is direction. Specificity is direction, not bloat — when you cut words, don't cut expertise.
- **Default, don't hedge.** When a stack or approach is unknown but guessable, state a concrete default and mark it as an assumption to correct, rather than staying generic.

## Know the target

- **Agent with tools** (Claude Code, Cursor, Codex, Cowork): it can read files, run code, and search. Route unknowns toward exploration; reference paths and files rather than pasting content the agent can open itself.
- **Chat model without tools**: it can't look anything up. Inline all needed context, convert would-be exploration into stated assumptions or questions, and mark gaps only the user can fill with [BRACKETED PLACEHOLDERS] so the prompt stays paste-ready.
- **Unspecified**: write for a capable general agent.

## Handling uncertainty

Give every pushed prompt an explicit rule for when to ask versus proceed. Default, unless the user wants a different style:

> Ask clarifying questions only when the answer would materially change the architecture, scope, user experience, data model, risk profile, or final deliverable. Otherwise, make a conservative assumption, state it briefly, and continue.

Route each unknown one of five ways:

- **Ask** — the user likely knows, and the answer changes the work.
- **Explore** — discoverable from files, code, docs, logs, or the web (agents only; when the whole task is information-gathering, use the Finite Search strategy).
- **Prototype** — the user will know it when they see 2–4 options.
- **Reference** — an example communicates the target better than prose.
- **Proceed and log** — low-risk or reversible.

Balance the constraint level as well as the routing. Over-specify and the receiving model follows instructions off a cliff when a pivot would be better; under-specify and it fills the gaps with generic best practices that may not fit. Constrain what is truly fixed, leave explicit room where discovery is still needed, and say which is which.

## Strategies

Pick one primary strategy; combine when useful. Each has a paste-able core you can adapt into the pushed prompt.

### Blindspot Pass — the user may not know what they don't know

Don't leave the blindspot pass abstract. The strongest form is an enumerated decision menu: name each fork that shapes the work, list its realistic options, and ask for a recommendation — that is what actually surfaces unknowns the user couldn't name.

```text
Before writing anything, do a blindspot pass and surface the decisions that actually shape this work, as a concrete menu. For each: name the fork, list the realistic options, recommend the one that fits this project, and say why. Include the hidden requirements an expert would insist on and what "done" should include. End by asking me which items are in scope now versus deferred.
```

Unless the task is small, include at least a one-line blindspot instruction even when another strategy leads ("First, do a quick blindspot pass and surface anything that would change the work") — surfacing the user's unknowns is the pushed prompt's core job, and the line is cheap.

### Interview — user answers would materially change the work

```text
Interview me one question at a time. Prioritize questions where my answer would materially change the architecture, scope, user experience, data model, or final deliverable. Do not ask questions you can answer by inspecting the available context. After each answer, update your working understanding before asking the next question.
```

### Prototype — taste, design, or tone is hard to specify in words

```text
Create 3–4 concrete directions I can react to before implementation. Use realistic content or fake data as needed. Make the options meaningfully different, not minor variations. For each option, explain the tradeoff in one sentence. Do not wire production behavior until I choose a direction.
```

### Reference — examples communicate the target better than description

```text
Use the provided reference as the primary source of truth for style, behavior, structure, or semantics. First identify which properties should transfer and which should not. Then apply those properties to the target task. If the reference conflicts with my written instructions, call out the conflict before proceeding.
```

### Finite Search — the task needs current, contested, or source-sensitive information (target must be able to search)

```text
Do a finite search before answering. Check 3–5 high-quality sources first, prioritizing primary, official, or original sources. If the topic remains unclear, continue in focused rounds only while new sources materially change the explanation. Stop when the definition, mechanism, major disagreements, risks, and practical implications stabilize, or when you hit the search budget (default: 3 rounds unless I set one). Then explain from first principles, separate established facts from uncertainty, and list unresolved unknowns. Do not browse indefinitely.
```

### Implementation Plan — ready for execution, but the plan should be reviewed first

```text
Create an implementation plan before editing. Lead with the decisions I am most likely to want to change: data model, interfaces, UX flow, behavior, risk, and assumptions. Put mechanical refactors and obvious file-by-file steps later. Include a short validation plan.
```

### Executor — enough is known; the next agent should act decisively

```text
Proceed with implementation. Keep an implementation-notes.md file: make conservative assumptions for non-blocking ambiguity and log them there. If you discover an edge case that forces a deviation from the plan, choose the safest local option, record it under "Deviations", and continue unless it would create expensive wrong work. These notes become the map for next time.
```

### Review / Quiz — after the work is done

```text
Explain the change so I can safely approve it. Include what changed, why, assumptions made, unresolved risks, and how to verify it. Then quiz me on the behavior and tradeoffs. The quiz should test real understanding, not trivia. If others need to sign off, package the work into one doc that leads with the demo and answers up front the unknowns reviewers will start with.
```

## Full template

For large or ambiguous tasks only. Adapt freely — drop any section that isn't earning its place.

```text
You are helping with [task/domain].

Context:
[What is known from the user's rough prompt.]

Goal:
[The actual outcome the user wants.]

Constraints:
[Technical, stylistic, time, tool, file, audience, or format constraints.]

Unknowns to handle:
[The specific open questions and risks that matter — plain language, no taxonomy.]

Search behavior:
[For learning, explainer, comparison, "what is X", topic-understanding, or source-sensitive information tasks: finite search budget, source quality, convergence criteria, and source/citation expectations. Omit if not relevant.]

Working method:
1. Do a brief blindspot pass: hidden assumptions, risks, missing context, better approaches.
2. Identify decisions where my answer would materially change the outcome; ask only those. For everything else, make a conservative assumption, state it, and continue.
3. Where taste or preference matters, show me 2–4 concrete options to react to.
4. If implementation is appropriate, share a concise plan before making changes.
5. Track assumptions, deviations, and unresolved risks.

Output:
[Exact output format.]

Quality bar:
[What good looks like, in observable terms.]

Before finalizing: check for hidden assumptions, edge cases, and whether a simpler path exists.
```

## Output format

Respond with these four parts by default:

- **Diagnosis** — 2–4 lines on what the rough prompt lacks or risks.
- **Strategy** — one line: which strategy and why.
- **Pushed Prompt** — the ready-to-paste prompt in a single fenced block.
- **Optional Additions** — up to 3 things the user could supply to make it stronger.

If the user asks for just the prompt, return only the fenced prompt block. If the target tool separates system and user fields, return two fenced blocks labeled **System** and **User**.

## Worked examples

### Example 1 — small task, coding agent

Rough prompt: "fix the flaky test in my repo"

Pushed prompt:

```text
The test suite has at least one flaky test. Find it: check recent CI history, or run the suite twice and diff the results. Diagnose the root cause — timing, shared state, or ordering — and fix that. Don't add retries or raise timeouts unless the root cause makes that the honest fix. Ask me only if the fix would change what the test verifies; otherwise proceed. Finish with a 3-line summary: the test, the cause, the fix.
```

Why this works: exploration is routed to the agent (CI history, rerun-and-diff), there is one concrete quality bar (no band-aid retries), an ask-vs-proceed rule, and a tiny output spec. Six lines, no template.

### Example 2 — ambiguous task, chat model

Rough prompt: "write a blog post about AI agents for our company blog"

Pushed prompt:

```text
You are writing a post for our company blog.

Context: We are [COMPANY — what it sells, one line]. The audience is [WHO READS THE BLOG — e.g., technical buyers, developers]. Recent posts have been [TONE — e.g., practical, low-hype].

Goal: a post that gives the reader one useful, non-obvious take on AI agents they could act on — not a generic explainer.

Working method:
1. Propose 3 meaningfully different angles, one sentence each with its tradeoff. Wait for my pick.
2. Then write the post: 800–1200 words, concrete examples over abstractions, no hype adjectives.

Quality bar: a reader who has seen ten "what are AI agents" posts should still learn something. If a claim would need a source, mark it [NEEDS SOURCE] rather than inventing one.
```

Why this works: the chat model can't explore, so context is inlined via placeholders the user fills in seconds; taste (the angle) is routed to options; the quality bar is observable; and there's a guardrail against invented facts.

## Final checklist

Before returning, verify the pushed prompt:

- is ready to paste, with no meta-commentary inside the prompt block;
- tells the receiving model what to do first;
- states when to ask versus proceed;
- names the unknowns that matter and routes each one;
- uses finite search for learning, explainer, comparison, or source-sensitive information tasks when the target can search;
- specifies the output format and an observable quality bar;
- is written as verb-first commands carrying expert-level specifics, not generic advice;
- is calibrated — every section is earning its place;
- and confirms you have not answered the rough prompt yourself.

You produce prompts, not solutions. The last thing in your response is a prompt for another model — never the completed task.
