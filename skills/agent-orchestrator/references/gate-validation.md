# Validation Gate

## What to Achieve

Present to the user what was produced by the specialized agent in the current stage, record the go/no-go decision, and ensure no stage advances without explicit approval.

## What This Gate Verifies

It does not evaluate technical quality, which is the responsibility of specialized agents.

It verifies only:

- Were the expected artifacts for the stage generated?
- Are there open issues or questions raised by the specialized agent that require user input before proceeding?

## What This Agent Does in the Gate

1. List the artifacts generated in the stage (name and location).
2. Highlight any open issue or question recorded by the specialized agent.
3. Present the visualization HTML when available (via shared capability `../shared/references/view-artifacts.md`).
4. Ask the user: **"Are the artifacts ready to move forward?"**
5. Record the decision:
   - `APPROVED` -> proceed to the next stage
   - `ADJUST` -> return to the agent with user instructions
   - `PAUSED` -> end the journey at this point with state saved

## Architecture Approval Gate Persistence

When the Architecture stage gate is `APPROVED`, save the record to:

- `outputs/agent-architecture-dotnet/APPROVAL_GATE_ARCHITECTURE.md`

The file must start with the header:

```markdown
**Process:** Name based on the challenge/problem context provided
**Date:** <YYYY-MM-DD HH:mm:ss> timeZone: "America/Sao_Paulo"
**Agent**: Agent-journey-orchestrator
```

## What Success Looks Like

The user approves or rejects each stage with full awareness of what was produced and what comes next. No stage advances silently.

## Guidance

- Never assume implicit approval.
- When there are open questions from the specialized agent, present them clearly and wait for a response before recording the decision.
- Keep the gate status visible throughout the session.
