# ChallengerApps — AI Agent Journey for Software Architecture

A GitHub Copilot agent workflow for end-to-end software architecture design, powered by the **BMAD** framework. Three specialized agents collaborate to guide you from a raw problem description to a ready-to-implement architectural blueprint.

---

## What This Project Does

The workflow takes an unstructured problem description (text, images, documents) and produces structured, traceable engineering artifacts through three sequential stages:

```text
User Input
    │
    ▼
[Orchestrator] — collects context, drives flow, controls gates
    │
    ├──► [Advisor]    → Architectural Discovery (AS-IS)
    │
    └──► [Architect]  → Target Architecture Proposal (TO-BE + SDD)
```

No stage advances without your explicit approval.

---

## Agents

| Agent | File | Role |
| --- | --- | --- |
| **Orchestrator** | `.github/agents/orchestrator.agent.md` | Drives the full journey, collects context, mediates between agents, controls validation gates |
| **Advisor** | `.github/agents/advisor.agent.md` | Analyzes inputs to produce a traceable AS-IS view (flow, systems, integrations, risks, gaps) |
| **Architect** | `.github/agents/architect.agent.md` | Consumes discovery artifacts to produce the TO-BE blueprint, architectural decisions, guidelines, and SDD |

---

## Project Structure

```text
.github/
  agents/               # Agent wrappers (Copilot entry points)
  instructions/         # BMAD instruction templates
skills/
  agent-advisor/        # Advisor SKILL.md + references
  agent-architect/      # Architect SKILL.md + references
  agent-orchestrator/   # Orchestrator SKILL.md + references
  shared/               # Shared capabilities (view-artifacts, HTML template)
outputs/                # Generated artifacts per agent run
_bmad/                  # BMAD framework configuration and modules
_design/                # Agent design documents (non-runtime, precursors to SKILL.md)
_spec/                  # Project specifications
```

---

## Artifacts Produced

### Discovery stage (`outputs/agent-discovery/`)

| File | Description |
| --- | --- | --- |
| `discovery_summary.md` | Executive summary — problem scope and stakeholders |
| `asis.md` | AS-IS description with process flow and integration maps (Mermaid diagrams) |
| `gaps_and_risks.md` | Gaps and risks table with impact classification |
| `open_questions.md` | Critical validation checklist before architecture |
| `evidence_map.md` | Traceability table linking inputs to conclusions |
| `tech_stack_manifest.json` | Flat inventory of identified systems and technologies |

### Architecture stage (`outputs/agent-architecture-dotnet/`)

| File | Description |
| --- | --- |
| `architecture_blueprint.md` | Technical blueprint — layers, components, integrations |
| `architectural_decisions.md` | Justified architectural decisions with trade-offs |
| `implementation_guidelines.md` | .NET patterns, conventions, and best practices |
| `sdd.md` | Software Design Document with API contracts and data models |
| `trade_offs.md` | Architectural alternatives and their trade-offs |
| `APPROVAL_GATE_ARCHITECTURE.md` | Approval record generated when architecture is signed off |

---

## Requirements

- [VS Code](https://code.visualstudio.com/) 1.99+ with the [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) extension
- GitHub Copilot Chat enabled (agent mode required)
- GitHub account with Copilot access

---

## Prerequisites — Setting Up BMAD

The agent wrappers (`.github/agents/*.agent.md`) depend on the **BMAD framework** present in the `_bmad/` folder. Follow these steps when cloning the project or starting fresh.

### 1. Install BMAD in the project

BMAD is distributed as a set of files you copy into your project. Download or clone the BMAD release and copy the contents into `_bmad/`:

```bash
# Example using GitHub CLI to download the latest BMAD release
gh release download --repo bmad-ai/bmad-method --pattern "*.zip" --dir _bmad-tmp
# Extract and move the contents into _bmad/
```

Alternatively, copy the `_bmad/` folder from an existing working project.

> The `_bmad/` folder in this repository already contains the required configuration. If you cloned this repo, it is ready to use.

### 2. Enable agent mode in VS Code

1. Open VS Code Settings (`Ctrl+,` / `Cmd+,`).
2. Search for `chat.agent.enabled` and set it to `true`.
3. Restart VS Code.

### 3. Verify the agent wrappers are discoverable

Copilot reads agent wrappers from `.github/agents/`. Confirm the folder exists and contains the three `.agent.md` files:

```text
.github/agents/
  advisor.agent.md
  architect.agent.md
  orchestrator.agent.md
```

### 4. Configure the project

Edit `_bmad/config.yaml` to match your environment:

```yaml
document_output_language: English
output_folder: '{project-root}/_bmad-output'
```

Create `_bmad/config.user.yaml` for personal overrides (do not version this file):

```yaml
user_name: your-name
communication_language: English
```

---

## How to Use

### 1. Open the project in VS Code

```bash
code .
```

### 2. Open Copilot Chat in Agent mode

Click the Copilot icon in the sidebar → switch to **Agent** mode using the mode selector at the top of the chat panel.

### 3. Start the Orchestrator

In the agent selector, choose **`orchestrator`** and send a message describing your challenge:

``` text
@orchestrator I need to redesign the scheduling flow for our digital channel.
```

The orchestrator will guide you through context gathering, agent invocations, and approval gates.

### 4. Follow the journey

The flow is linear and gated:

1. **Context gathering** — The orchestrator asks targeted questions to understand the problem.
2. **Discovery** — The `advisor` agent is invoked. It produces AS-IS artifacts in `outputs/agent-discovery/`.
3. **Validation gate** — You review and approve (or request adjustments) before proceeding.
4. **Architecture** — The `architect` agent is invoked. It produces TO-BE artifacts in `outputs/agent-architecture-dotnet/`.
5. **Final gate** — You approve the architecture, which records an approval file.

### 5. View artifacts as HTML

At any gate, ask the orchestrator to render the artifacts:

``` text
Show me the artifacts
```

A single navigable HTML file is generated at `outputs/validation_view.html` with all Mermaid diagrams rendered.

---

## Publishing to GitHub

### Initialize and push

```bash
# Initialize local repository
git init
git add .
git commit -m "chore: initial commit"

# Create a repository on GitHub (via GitHub CLI or the GitHub website)
# Then connect and push:
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

### Using GitHub CLI (recommended)

```bash
# Install if needed: https://cli.github.com
gh auth login

# Create the repository and push in one step
gh repo create <repo-name> --private --source=. --remote=origin --push
```

### Recommended `.gitignore`

Create a `.gitignore` to exclude generated output files if you do not want to version them:

```gitignore
outputs/
_bmad-output/
_spec/
*.tmp.*
```

---

## License

This project does not ship a license by default. Add a `LICENSE` file via GitHub when creating the repository, or use:

```bash
gh repo edit --license mit
```
