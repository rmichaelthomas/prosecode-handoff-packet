---
name: prosecode-handoff-packet
description: >-
  Create continuity transfer objects for handing off a working session from
  one agent, model, or tool to another. Use when the user asks to create a
  handoff packet, hand off this session, prepare work for another agent,
  package context for Codex, Claude, ChatGPT, Gemini, Cursor, or summarize
  the working state. Produces a structured Markdown packet that preserves
  verified sources, inferred claims, locked decisions, open questions, active
  user corrections, retained context, and boundaries on what the next agent
  may honestly claim.
license: MIT
metadata:
  author: rmichaelthomas
  version: "0.1.0"
  provenance: "Prosecode continuity stack + Liminate session contracts + cross-agent handoff discipline"
---

# Prosecode Handoff Packet

## What this skill does

This skill creates a continuity transfer object: a structured Markdown packet that lets another agent, model, or tool continue from the current working state.

It is not a normal summary. A summary says what happened. A handoff packet says what the next agent needs in order to continue honestly, usefully, and safely.

A handoff packet is useful only if it preserves uncertainty as carefully as it preserves progress.

## When to use

Use this skill when the user asks for:

- "create a handoff packet"
- "handoff this session"
- "prepare this for another agent"
- "package this for Codex"
- "package this for Claude"
- "package this for ChatGPT"
- "package this for Gemini"
- "package this for Cursor"
- "summarize the working state"
- "handoff packet"
- "continue this in another model"
- "make this portable"

Also use when:

- a session is ending
- the user is moving from one model or tool to another
- a long session needs to be preserved
- an agent is about to lose context
- a project has reached a decision checkpoint
- a session contract needs to be handed to another agent

## When not to use

Do not use this skill on:

- greetings
- simple one-turn questions
- ordinary summaries where continuity is not needed
- purely creative writing unless the user asks for handoff
- system messages
- tool outputs alone
- private chain-of-thought requests

## Core rule

> Transfer what can be honestly transferred. Mark what cannot.

Do not pretend to transfer hidden reasoning, unavailable files, private chain-of-thought, or sources you have not actually read. Transfer visible decisions, verified sources, active corrections, open questions, assumptions, and current work state. Mark the basis for each claim.

## Relationship to the Prosecode stack

Use this skill alone when needed. If other Prosecode skills are available, preserve their useful state.

### If `session-contracts` is available

Read or infer:

- active session corrections
- verified sources
- source-state
- claim-basis
- tracked decisions
- open questions
- contract path
- any `.limn` deltas emitted this session

Preserve active corrections prominently. They are high-priority handoff material.

### If `prosecode-intent-compiler` is available

Include:

- active intent verb
- filled slots
- unresolved slots
- amber or contradiction flags
- secondary intent if relevant
- inferred response posture

If no Intent IR is available, infer the current intent from the visible conversation and mark it as inferred.

### If `prosecode-heap-pager` is available

Include:

- retained context
- paged summaries
- evicted context notices if available
- context that must not be evicted
- source blocks that should remain high-retention

If no heap pager output exists, create a compact "Context to Preserve" section from visible conversation only.

### If none of the other skills are available

Still produce a handoff packet from the visible conversation. Mark source and context limits honestly.

## Required packet sections

Every handoff packet must include these sections, in this order:

```md
# Handoff Packet

## 1. Current Goal
## 2. Current State
## 3. Active Intent
## 4. Verified Sources
## 5. Inferred Claims
## 6. Locked Decisions
## 7. Open Questions
## 8. Active User Corrections
## 9. Context to Preserve
## 10. Work Completed
## 11. Work Not Yet Completed
## 12. Do Not Claim Beyond This
## 13. Recommended Next Action
## 14. Handoff Notes for the Receiving Agent
```

Each section must be concise but specific.

## Section guidance

### 1. Current Goal

State the actual goal of the working session in one to three sentences.

### 2. Current State

Describe where the work stands right now.

### 3. Active Intent

Use the intent compiler vocabulary where possible:

- `explain`
- `create`
- `transform`
- `analyze`
- `decide`
- `plan`
- `fix`

Example:

```md
Primary intent: create
Target/artifact: new Prosecode skill repo
Status: approved direction; build prompt ready
Basis: inferred from visible user instruction
```

### 4. Verified Sources

List only sources actually read or inspected.

For each source include:

- source name
- source type
- verification state: verified / scanned / referenced / unavailable
- what was verified from it

Do not list a source as verified just because the user linked it.

### 5. Inferred Claims

List claims that seem true from the conversation but are not directly verified.

Use direct language:

```md
- Inferred: The user wants this repo to match the style of the other Prosecode repos.
  - Basis: User instruction and existing repo naming pattern.
```

### 6. Locked Decisions

List decisions the user explicitly approved or that were clearly locked. Do not include suggestions that were not approved.

### 7. Open Questions

List unresolved questions. If none are visible, say:

```md
No open questions visible in the current context.
```

### 8. Active User Corrections

Preserve behavioral corrections and engagement posture. Examples:

- no deferrals
- verify against source, not memory
- plain English
- execute, do not over-propose
- stay focused
- answer in stated order
- do not add extra deliverables beyond the requested set

This section is mandatory because user corrections are one of the most important things to hand off between models.

### 9. Context to Preserve

Include context that the next agent should retain raw or near-raw, such as exact approved naming, exact file lists, exact repo relationships, exact constraints, and exact user language that matters.

### 10. Work Completed

List what has already been done so the next agent does not repeat it.

### 11. Work Not Yet Completed

List remaining work. Do not over-expand into a project plan unless the user asked for one.

### 12. Do Not Claim Beyond This

This is the epistemic boundary section. Include:

- what has not been verified
- what source access was missing
- what the next agent must inspect before making claims
- what should be described as inferred rather than known

This section is required.

### 13. Recommended Next Action

Give one direct next action for the receiving agent.

### 14. Handoff Notes for the Receiving Agent

Write short operational notes to the next model or tool. Use a direct, practical tone. Do not flatter, invent fictional continuity, or add vague encouragement.

## Output rules

Output a Markdown packet.

Default filename:

```text
handoff-packet.md
```

If a target model or tool is named, include it in the title or metadata:

- `codex-handoff-packet.md`
- `claude-handoff-packet.md`
- `chatgpt-handoff-packet.md`

Do not create a `.limn` file by default.

However, if `session-contracts` is active or the user asks for contract integration, also emit an optional `.limn` contract delta.

Example:

```limn
add "handoff-packet-created" to tracked-decisions
remember a string called handoff-state with "ready"
```

Do not invent Liminate verbs. Use only valid base vocabulary unless a loaded pack is explicitly available.

## Honesty constraints

- Do not claim to have read files that were only linked.
- Do not convert assumptions into decisions.
- Do not convert unresolved questions into completed work.
- Do not preserve hidden chain-of-thought.
- Do not claim the next agent will have access to unavailable tools.
- Do not omit active user corrections.
- Do not summarize away source-state.
- Do not bury uncertainty at the end.

## Receiving-agent usefulness test

Before finalizing a handoff packet, ask internally:

1. Could another agent continue from this without asking the user to repeat themselves?
2. Does the packet clearly distinguish verified, inferred, open, and unavailable information?
3. Does it preserve the user's corrections and engagement posture?
4. Does it prevent the next agent from overclaiming?
5. Does it avoid bloated transcript recap?
6. Does it name the immediate next action?

If any answer is no, revise the packet.

## Human usefulness test

Before finalizing, also ask internally:

1. Would the user recognize this as an accurate state of the work?
2. Is anything important missing?
3. Is anything overstated?
4. Is the packet easy to scan?
5. Would this reduce the user's burden in the next tool?

## Example packet shape

```md
# Handoff Packet

**Created for:** Claude
**Created by:** Codex
**Session / project:** Dashboard navigation redesign

## 1. Current Goal

Design a clearer dashboard navigation model for a SaaS admin app.

## 2. Current State

The user approved a left-sidebar structure and rejected a top-tab layout. No implementation has started.

## 3. Active Intent

Primary intent: create
Target/artifact: navigation design direction
Status: decisions locked; implementation pending
Basis: inferred from visible conversation

## 4. Verified Sources

| Source | Type | State | What was verified |
|---|---|---|---|
| User-provided screenshots | visible conversation | scanned | Current nav has duplicate account links and unclear grouping. |

## 5. Inferred Claims

- Inferred: The user prefers operational clarity over decorative redesign.
  - Basis: User rejected "marketing-style" layout language.

## 6. Locked Decisions

- Decision: Use a persistent left sidebar.
  - Basis: User explicitly approved it.

## 7. Open Questions

- Question: Should billing live under Account or Workspace?
  - Why it matters: It affects navigation grouping.

## 8. Active User Corrections

- Correction: Keep it practical and do not over-explain.
  - Meaning for next agent: Lead with concrete design choices.

## 9. Context to Preserve

- Context: The user rejected the top-tab approach.
  - Why it matters: Do not re-propose it.

## 10. Work Completed

- Completed: Compared sidebar and top-tab models.

## 11. Work Not Yet Completed

- Remaining: Produce final navigation labels and grouping.

## 12. Do Not Claim Beyond This

- Boundary: Do not claim implementation feasibility.
  - Reason: The codebase has not been inspected.

## 13. Recommended Next Action

Draft the final sidebar information architecture.

## 14. Handoff Notes for the Receiving Agent

Continue from the locked sidebar decision. Preserve the open billing placement question.
```
