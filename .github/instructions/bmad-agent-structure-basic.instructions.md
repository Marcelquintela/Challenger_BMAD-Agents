---
description: "Use quando o usuario iniciar um projeto novo e pedir a estrutura basica de agentes BMAD (pastas de output e wrappers). Nao cria skills nesta etapa."
applyTo: "**"
---

# Setup Basico de Estrutura BMAD

Use este fluxo quando o usuario pedir para criar agentes BMAD em projeto novo.

## O que criar

Para cada agente informado:

1. Pasta de output:
   - `outputs/agent-<slug>/`
2. Wrapper do agente:
   - `.github/agents/<slug>.agent.md`

Nao criar skills nesta etapa. As skills serao criadas automaticamente depois.

## Regras obrigatorias

- Usar `slug` em kebab-case minusculo.
- Em nomes de jornada, manter o nome do wrapper com ponto (exemplo: `<slug>.agent.md`).
- O wrapper deve apontar para a skill esperada em `skills/agent-<slug>/SKILL.md`.
- Se pasta ou arquivo ja existir, pedir confirmacao antes de sobrescrever.
- Se houver varios agentes no pedido, criar estrutura para todos.

## Estrutura minima do wrapper

```md
---
description: <descricao detalhada do papel do agente>
handoffs:
  - label: Voltar ao Orquestrador
    agent: journey.orchestrator
      prompt: O agente <slug> concluiu a execucao. Artefatos disponiveis em outputs/agent-<slug>/.
---

#file:../../skills/agent-<slug>/SKILL.md

Voce e este agente. Siga exatamente as instrucoes do SKILL.md acima.

## User Input

```text
$ARGUMENTS
```

Se houver input acima, use como contexto inicial. Caso contrario, solicite os insumos necessarios para iniciar.
```

## Resposta final esperada ao usuario

Sempre retornar:

- Agentes criados
- Arquivos criados em `.github/agents/`
- Pastas criadas em `outputs/`
- Aviso: skills serao geradas automaticamente na proxima etapa