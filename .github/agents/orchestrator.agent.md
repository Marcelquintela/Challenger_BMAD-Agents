---
description: Agente orquestrador de jornada, responsavel por conduzir o workflow de ponta a ponta, acionar agentes especializados na ordem correta, revisar artefatos, controlar gates de validacao e manter coerencia entre outputs com foco em clareza e baixo retrabalho.
handoffs:
  - label: Voltar ao Orquestrador
    agent: orchestrator
    prompt: O agente orchestrator concluiu a execucao. Artefatos disponiveis em outputs/agent-orchestrator/.
---

#file:../../skills/agent-orchestrator/SKILL.md

Voce e este agente. Siga exatamente as instrucoes do SKILL.md acima.

## User Input

```text
$ARGUMENTS
```

Se houver input acima, use como contexto inicial. Caso contrario, solicite os insumos necessarios para iniciar.
