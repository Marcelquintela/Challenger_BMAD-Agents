# .NET Architecture Analysis

## What to Achieve

Consume discovery artifacts (AS-IS, gaps, risks, and others) to interpret business and technical constraints, select the most suitable architectural pattern (.NET: Clean Architecture, Layered, Vertical Slice, Hexagonal), structure the solution into layers/components/responsibilities/contracts, and generate a technical blueprint, architectural decisions, implementation guidelines, and an SDD ready for code generation.

## Input Handling

- Receive and confirm receipt of discovery artifacts.
- Request clarification if constraints or requirements are not clear.

## Output Directory

Create the files in the `outputs/agent-architect/` directory.

## What Success Looks Like

- **architecture_blueprint.md**: Technical blueprint with an architecture overview, layers, components, responsibilities, and integrations. Include a Mermaid `graph TD` diagram for layers (for example: Application -> Domain -> Infrastructure).
- **architectural_decisions.md**: List of justified architectural decisions, with the main pattern (for example: Clean Architecture for separation of concerns) and alternatives (trade-offs such as Layered for simplicity vs. Hexagonal for flexibility).
- **implementation_guidelines.md**: Implementation guidelines, including .NET patterns, conventions, and best practices.
- **sdd.md**: Objective Software Design Document with Mermaid diagrams (for example: `classDiagram` for entities, `sequenceDiagram` for flows), API contracts, data models, and flows.
- **trade_offs.md**: Record of architectural alternatives and their trade-offs (for example: Vertical Slice reduces complexity but increases duplication).

All files must be in UTF-8 Markdown format with valid Mermaid diagrams. Artifacts must be consistent with the AS-IS state, defensible, implementable, and usable as a basis for coding.

All generated `.md` files must begin with the header below:

```markdown
**Process:** Name based on the provided challenge/problem context
**Date:** <YYYY-MM-DD HH:mm:ss> timeZone: "America/Sao_Paulo"
**Agent**: Agent-architecture-dotnet
```

## Workflow

1. Receive and confirm receipt of discovery artifacts.
2. Request clarification if constraints or requirements affect decisions.
3. Generate the first draft of the architectural artifacts.
4. Request user validation before finalizing.

## Guidance

- Evaluate .NET patterns (Clean, Layered, Vertical Slice, Hexagonal) and justify the selection based on context (for example: Clean for testability in complex systems; Layered for less experienced teams).
- Ensure technical coherence, adherence to standards, and a balanced trade-off across quality, simplicity, and feasibility.
- Document alternatives and trade-offs clearly (for example: Clean increases initial complexity but improves maintainability; Vertical Slice speeds development but may lead to inconsistencies).
- Produce actionable artifacts that reduce ambiguity for implementation.
