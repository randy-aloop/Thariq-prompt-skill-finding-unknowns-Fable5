# Thariq's X article Prompt Skill: Finding Unknowns for Fable 5

This repository contains `prompt-push`, a skill inspired by Thariq's X article, [A Field Guide to Fable: Finding Your Unknowns](https://x.com/trq212/article/2073100352921215386).

The article's central idea is simple and powerful: prompts are the map, real work is the territory, and the gap between them is made of unknowns. `prompt-push` turns that idea into a practical skill for rewriting rough user intent into a stronger prompt for another LLM or coding agent.

## What This Skill Does

`prompt-push` does not solve the user's task directly. It produces a better prompt for the next model.

Use it when you explicitly want to turn a rough prompt into a sharper, ready-to-paste prompt for an LLM or agent. The skill:

- extracts the actual goal, context, constraints, and target model
- classifies unknowns internally
- decides whether the next model should ask, explore, prototype, reference, proceed, or search finitely
- calibrates prompt size to task size
- produces a prompt with a clear working method, output format, and quality bar

## Why It Exists

Thariq's article frames agentic work around a recurring failure mode: the model is forced to make decisions inside gaps the user did not know to clarify.

Those gaps show up as:

- **Known knowns**: what the user already said
- **Known unknowns**: open questions the user can see
- **Unknown knowns**: taste, preferences, or standards the user may recognize only after seeing options
- **Unknown unknowns**: hidden constraints, risks, or better approaches the user has not considered

`prompt-push` helps the next model handle those gaps deliberately instead of guessing through them.

## Skill Diagram

```mermaid
flowchart TD
    A["Rough user prompt"] --> B["Extract the map\nGoal, domain, audience, target model,\noutput, constraints, environment, preferences"]
    B --> C["Classify unknowns internally\nKnown knowns / known unknowns /\nunknown knowns / unknown unknowns"]
    C --> D["Know the target"]
    D --> D1["Agent with tools\nRoute unknowns to exploration\nReference paths/files"]
    D --> D2["Chat model without tools\nInline context\nUse bracketed placeholders"]
    D --> D3["Unspecified\nWrite for capable general agent"]

    D1 --> E["Calibrate weight"]
    D2 --> E
    D3 --> E

    E --> E1["Small task\n3-6 lines"]
    E --> E2["Medium task\nGoal + context + 2-3 unknowns + method"]
    E --> E3["Large/ambiguous task\nFull template"]

    E1 --> F["Route uncertainty"]
    E2 --> F
    E3 --> F

    F --> F1["Ask\nUser knows and answer changes work"]
    F --> F2["Explore\nDiscoverable from files/code/docs/logs/web"]
    F --> F3["Prototype\nUser knows after seeing options"]
    F --> F4["Reference\nExample communicates better than prose"]
    F --> F5["Finite search\nSource-sensitive learning or comparison"]
    F --> F6["Proceed and log\nLow-risk or reversible"]

    F --> G["Choose primary strategy"]
    G --> G1["Blindspot Pass"]
    G --> G2["Interview"]
    G --> G3["Prototype"]
    G --> G4["Reference"]
    G --> G5["Finite Search"]
    G --> G6["Implementation Plan"]
    G --> G7["Executor"]
    G --> G8["Review / Quiz"]

    G1 --> H["Write pushed prompt"]
    G2 --> H
    G3 --> H
    G4 --> H
    G5 --> H
    G6 --> H
    G7 --> H
    G8 --> H

    H --> I["Final checklist\nPaste-ready, first action clear,\nask/proceed rule, routed unknowns,\nfinite search when useful,\noutput format, quality bar,\nno solved task"]
    I --> J["Return response\nDiagnosis / Strategy / Pushed Prompt / Optional Additions"]
```

## Finite Search Behavior

For learning, explanation, comparison, "what is X", and source-sensitive information tasks, the skill pushes the receiving model to search deeply but finitely.

It should not ask for infinite search or "search until the bottom." Instead, it asks the model to:

- check a bounded set of high-quality sources
- prioritize primary, official, or original references
- continue only while new sources change the explanation
- stop when the definition, mechanism, disagreements, risks, and implications stabilize
- separate established facts from uncertainty
- list remaining unknowns

This makes the receiving model more curious without letting it browse forever.

## Codex Version

The Codex package lives at:

```text
codex/prompt-push/SKILL.md
```

Copy that file into your Codex skills directory:

```text
$CODEX_HOME/skills/prompt-push/SKILL.md
```

Then restart Codex so the skill metadata is loaded.

## Claude Version

A Claude-oriented package lives at:

```text
claude/prompt-push/SKILL.md
```

Use or upload the `claude/prompt-push` folder in your Claude skill setup. This version preserves the same prompt-push workflow while removing Codex-specific installation assumptions and wording the target as Claude, Claude Code, another LLM, or another coding agent.

## Invocation

The skill is intentionally narrow-triggered. Invoke it explicitly:

```text
run $prompt-push:
Is this eval and memory layer built according to the latest AI advancements?
```

The output should be a better prompt for another model, not the answer to the question.

## Example Output Shape

By default, `prompt-push` returns:

1. **Diagnosis**: what the rough prompt lacks or risks
2. **Strategy**: the prompting strategy selected
3. **Pushed Prompt**: a ready-to-paste prompt
4. **Optional Additions**: extra context the user could provide

## Repository Contents

- `codex/prompt-push/SKILL.md`: the Codex skill package
- `claude/prompt-push/SKILL.md`: the Claude-oriented skill package
- `README.md`: this overview and attribution

## Attribution

This repository is an independent skill package inspired by Thariq's public article on finding unknowns while working with Claude Fable 5:

[https://x.com/trq212/article/2073100352921215386](https://x.com/trq212/article/2073100352921215386)

It is not an official Anthropic, Claude, X, or Thariq project.
