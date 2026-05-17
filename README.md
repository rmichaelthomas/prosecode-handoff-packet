# prosecode-handoff-packet

Packages a working session so another agent can continue without the user repeating themselves.

It's not a summary. It transfers operational state: current goal, current state, verified sources, inferred claims, locked decisions, open questions, user corrections, context to retain, work completed, work remaining, and the boundaries on what the next agent may claim.

## What it does

Long or consequential sessions often fail at the handoff point. The next model repeats work, misses a correction, flattens an unresolved question into a conclusion, or claims certainty it doesn't have.

A handoff packet is useful only if it preserves uncertainty as carefully as it preserves progress. The skill produces a structured Markdown packet that lets another agent resume work without overstating what was verified, losing the user's corrections, repeating work, flattening open questions into conclusions, or losing the current intent.

## Example

A summary says:

> Here is what happened.

A handoff packet says:

> Here is what the next agent needs in order to continue honestly, usefully, and safely from the current state.

A summary may be chronological. A handoff packet is operational. A summary may smooth over uncertainty. A handoff packet must preserve it.

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

## Part of the Liminate family

Liminate is a prose-as-syntax programming language where plain English sentences execute directly. These five repos form a system for writing, verifying, and transferring structured reasoning.

| | Repo | What it does |
|---|---|---|
| | [liminate](https://github.com/rmichaelthomas/liminate) | The language and interpreter. Bounded vocabulary, deterministic execution, domain packs. |
| | [liminate-session-contracts](https://github.com/rmichaelthomas/liminate-session-contracts) | Tracks verified sources, inferred claims, locked decisions, and user corrections as executable `.limn` contracts. |
| | [prosecode-prompt-compiler](https://github.com/rmichaelthomas/prosecode-prompt-compiler) | Compiles user prompts into structured intent before the agent responds. Seven verbs, twenty-four slots. |
| | [prosecode-context-pager](https://github.com/rmichaelthomas/prosecode-context-pager) | Scores conversation history against current intent. Decides what to keep, summarize, or drop. |
| **← this repo** | [**prosecode-handoff-packet**](https://github.com/rmichaelthomas/prosecode-handoff-packet) | **Packages a working session for another agent to continue — preserving what was verified and what wasn't.** |

→ [onesurface.org/liminate](https://onesurface.org/liminate)

You can use this skill by itself. When the other Prosecode skills are present, the packet preserves their useful state instead of trying to reconstruct it from memory.

## Install

This skill follows the [agentskills.io](https://agentskills.io) SKILL.md standard. Any compliant agent can load it.

```bash
# Claude Code — all projects
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.claude/skills/prosecode-handoff-packet

# Claude Code — one project
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git .claude/skills/prosecode-handoff-packet

# Codex CLI
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.codex/skills/prosecode-handoff-packet

# Gemini CLI
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git ~/.gemini/skills/prosecode-handoff-packet

# Any SKILL.md-compatible agent
git clone https://github.com/rmichaelthomas/prosecode-handoff-packet.git .agents/skills/prosecode-handoff-packet
```

## How it works

### Use

Ask the agent for a handoff packet when a session needs to move somewhere else or continue later:

> create a handoff packet
> handoff this session
> prepare this for another agent
> package this for Codex
> package this for Claude
> summarize the working state

The skill writes a Markdown packet. The default filename is `handoff-packet.md`. If the target tool is named, the filename can include it: `codex-handoff-packet.md`, `claude-handoff-packet.md`, `chatgpt-handoff-packet.md`.

### Output format

Every packet uses the same fourteen sections in the same order:

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

### Honesty constraints

The packet's value comes from preserving uncertainty. Sections 4, 5, and 12 carry most of the weight: verified-versus-inferred is recorded explicitly, and the "do not claim beyond this" boundary is the contract the receiving agent inherits. A handoff packet that smooths over an open question into a confident conclusion has destroyed its own purpose.

## License

MIT. See [LICENSE](LICENSE).
