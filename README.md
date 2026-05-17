# prosecode-handoff-packet

Create continuity transfer objects for handing off work between agents, models, and tools.

## What it is

`prosecode-handoff-packet` is an Agent Skill for producing a structured Markdown packet that lets another agent continue a working session honestly.

It is not a normal summary. It transfers the operational state of the work: current goal, current state, verified sources, inferred claims, locked decisions, open questions, user corrections, context that must be retained, completed work, remaining work, and the boundaries on what the next agent may claim.

A handoff packet is useful only if it preserves uncertainty as carefully as it preserves progress.

## Why it exists

Long or consequential sessions often fail at the handoff point. The next model may repeat work, miss a correction, flatten an unresolved question into a conclusion, or claim certainty it does not have.

This skill exists so a user can move from one model or tool to another without re-explaining the session and without letting the new agent inherit fake certainty.

A handoff packet helps another model resume work without:

- overstating what was verified
- losing the user's corrections
- repeating work
- flattening open questions into conclusions
- losing the current intent
- ignoring context boundaries

## How it fits the Prosecode stack

`prosecode-handoff-packet` is part of the Prosecode continuity stack:

- [`session-contracts`](https://github.com/rmichaelthomas/session-contracts) records verified versus inferred claims, locked decisions, open questions, and user corrections.
- [`prosecode-intent-compiler`](https://github.com/rmichaelthomas/prosecode-intent-compiler) identifies the active intent, required slots, missing slots, contradictions, and response posture.
- [`prosecode-heap-pager`](https://github.com/rmichaelthomas/prosecode-heap-pager) decides which context should be retained, paged, or evicted as the session grows.
- `prosecode-handoff-packet` packages the live state for another agent.

You can use this skill by itself. When the other Prosecode skills are present, the packet should preserve their useful state instead of trying to reconstruct it from memory.

## Summary vs. handoff packet

A summary says:

> Here is what happened.

A handoff packet says:

> Here is what the next agent needs in order to continue honestly, usefully, and safely from the current state.

A summary may be chronological. A handoff packet is operational.

A summary may smooth over uncertainty. A handoff packet must preserve it.

A summary may mention decisions and sources casually. A handoff packet marks what is verified, inferred, open, unavailable, completed, and not yet completed.

## Install

This skill follows the `SKILL.md` agent skill pattern. Clone it into the skill location used by your agent.

```bash
# Claude Code - all projects
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.claude/skills/prosecode-handoff-packet

# Claude Code - one project
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git .claude/skills/prosecode-handoff-packet

# Codex CLI
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.codex/skills/prosecode-handoff-packet

# Gemini CLI
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.gemini/skills/prosecode-handoff-packet

# Universal .agents/skills
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git .agents/skills/prosecode-handoff-packet
```

## Use

Ask the agent for a handoff packet when a session needs to move somewhere else or continue later:

> create a handoff packet

> handoff this session

> prepare this for another agent

> package this for Codex

> package this for Claude

> summarize the working state

The skill writes a Markdown packet. The default filename is:

```text
handoff-packet.md
```

If the target tool is named, the filename can include it:

```text
codex-handoff-packet.md
claude-handoff-packet.md
chatgpt-handoff-packet.md
```

## Output format

Every packet uses the same section order:

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

The section names are intentionally plain. The receiving agent should be able to scan the packet and know what to continue, what not to repeat, what remains unresolved, and where the verification boundary begins.

## Example packet preview

```md
# Handoff Packet

**Created for:** Codex
**Created by:** Claude
**Session / project:** Review checkout flow refactor

## 1. Current Goal

Review the checkout flow refactor and identify source-backed regressions.

## 4. Verified Sources

| Source | Type | State | What was verified |
|---|---|---|---|
| src/checkout/CheckoutForm.tsx | repo file | verified | The submit handler now calls `createOrder` before validating the selected shipping option. |

## 5. Inferred Claims

- Inferred: The new order of operations may create invalid orders.
  - Basis: The form validation file has not yet been inspected.

## 12. Do Not Claim Beyond This

- Boundary: Do not claim the refactor is broken until the validation path and tests are inspected.
  - Reason: Only one source file has been verified.

## 13. Recommended Next Action

Inspect the validation helper and checkout tests before writing findings.
```

## License

MIT. See [LICENSE](LICENSE).
