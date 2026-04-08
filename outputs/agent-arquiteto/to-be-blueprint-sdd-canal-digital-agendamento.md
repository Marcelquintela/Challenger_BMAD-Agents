# TO-BE Blueprint Tecnico e SDD - Canal Digital de Agendamento de Exames

## 1) Resumo executivo TO-BE
A arquitetura TO-BE propoe um canal digital de autoatendimento 24x7 para agendamento de exames, com foco em reduzir dependencia operacional do telefone e mensagens, aumentar ocupacao de agenda e melhorar conveniencia do paciente.
A solucao sera implementada com um backend .NET orientado a dominio e integracoes, um portal web responsivo para jornada do paciente e uma camada de orquestracao para confirmacoes por mensageria.
O desenho prioriza entrega incremental em 3 meses, com MVP focado no escopo in aprovado: triagem basica, upload de pedido medico, selecao de unidade/horario e confirmacao automatica.
A arquitetura adota segregacao clara de responsabilidades entre experiencia digital, regras de negocio, integracoes HIS/RIS e compliance (LGPD/sigilo medico).
Para reduzir risco de prazo e equipe enxuta, a proposta evita overengineering e aplica modularizacao pragmatica (modular monolith com limites internos por contexto), mantendo caminho de evolucao para eventos assincronos e servicos separados quando necessario.

## 2) Principios arquiteturais
- Alinhamento ao objetivo de negocio: cada componente deve contribuir para migracao do agendamento ao digital e reducao da carga telefonica.
- Simplicidade orientada a prazo: priorizar menor complexidade operacional sem comprometer seguranca e rastreabilidade.
- Seguranca e privacidade by design: dados sensiveis protegidos em transito, em repouso e por controle de acesso contextual.
- Integracao resiliente: tolerancia a falhas de HIS, RIS e mensageria com retries controlados, idempotencia e fallback operacional.
- Consistencia observavel: cada etapa critica deve gerar eventos/auditoria para suporte operacional e compliance.
- Evolucao incremental: arquitetura preparada para extracao de modulos para microsservicos somente quando sinais reais de escala/acoplamento surgirem.
- Rastreabilidade requisito-decisao-implementacao: cada requisito funcional e nao funcional mapeado para componentes e ADRs.

## 3) Arquitetura alvo (componentes, responsabilidades, boundaries)
### Padrao arquitetural principal
- Padrao principal: Modular Monolith em .NET (Clean Architecture + Vertical Slices por caso de uso).
- Justificativa: reduz complexidade de distribuicao para equipe reduzida e prazo curto, mantendo isolamento logico por contexto e testabilidade.

### Contextos e boundaries
- Contexto Digital Frontdoor
  - Portal web responsivo para paciente.
  - Captura triagem basica, dados cadastrais minimos e upload de pedido medico.
- Contexto Orquestracao de Agendamento
  - Casos de uso: consultar disponibilidade, reservar horario, confirmar agendamento, cancelar reserva tecnica expirada.
  - Regras de negocio e validacoes transacionais.
- Contexto Integracao Clinico-Operacional
  - Adaptadores HIS/RIS para leitura/escrita necessaria ao agendamento.
  - Normalizacao de contratos e tratamento de erros externos.
- Contexto Comunicacao
  - Disparo de confirmacao e avisos via gateway de mensageria.
  - Politica de retries e dead-letter logico para falhas persistentes.
- Contexto Governanca e Compliance
  - Auditoria, trilha de acesso, retencao, politicas de mascaramento e consentimento/base legal.

### Componentes logicos
- Web App (BFF opcional): experiencia de agendamento e validacoes de entrada.
- API de Agendamento (.NET): endpoints de jornada e comandos de negocio.
- Application Layer: casos de uso em vertical slices.
- Domain Layer: entidades e regras de negocio.
- Integration Layer: conectores HIS/RIS/mensageria com politicas de resiliencia.
- Data Layer: persistencia operacional, documentos e trilha de auditoria.
- Background Workers: processamento assincrono de confirmacoes/retries.
- Observability Stack: logs estruturados, metricas tecnicas, traces e alertas.

## 4) Fluxos principais (agendamento e confirmacao)
### Fluxo de agendamento digital
1. Paciente acessa portal responsivo e inicia agendamento.
2. Sistema coleta triagem basica e valida campos obrigatorios.
3. Paciente envia pedido medico (upload) e sistema registra metadados/documento.
4. API consulta disponibilidade em tempo real via RIS/HIS conforme fonte de verdade definida.
5. Sistema apresenta unidades e horarios disponiveis.
6. Paciente seleciona unidade/horario.
7. Sistema executa reserva idempotente e confirma agendamento no backend.
8. Registro de auditoria e evento de confirmacao sao gerados.

### Fluxo de confirmacao automatica
1. Evento de agendamento confirmado aciona fila/processador assincrono.
2. Worker monta mensagem e envia ao gateway externo.
3. Em sucesso, status de notificacao e auditoria sao atualizados.
4. Em falha transitoria, aplica retry com backoff.
5. Em falha persistente, registra pendencia para tratamento operacional e rastreabilidade.

## 5) Estrategia de integracao HIS/RIS/mensageria (incluindo falhas, retries, idempotencia)
### Diretriz geral
- Integrar por camada de adaptadores com contratos internos estaveis para desacoplar variacoes de APIs externas.

### HIS
- Uso principal: validacoes administrativas e correlacao com prontuario quando requerido pelo fluxo.
- Estrategia de falha: timeout curto, retry para falhas transitorias, circuit breaker para degradacao controlada.
- Idempotencia: chave por requisicao de operacao (correlation-id + paciente + tipo exame + janela).

### RIS
- Uso principal: disponibilidade e confirmacao de agenda de imagem.
- Estrategia de concorrencia: evitar dupla reserva com operacao transacional/idempotente no limite da API disponivel.
- Falhas: retries para indisponibilidade temporaria; fallback para mensagem de indisponibilidade ao usuario sem confirmar agenda localmente se persistir.

### Gateway de mensageria
- Uso principal: confirmacao automatica e avisos de pendencia documental.
- Garantia de entrega: padrao at-least-once com controle de deduplicacao por message-id.
- Falhas: retries assincronos, fila de pendencias e reprocessamento monitorado.

### Politicas tecnicas transversais
- Correlation-id obrigatorio ponta a ponta.
- Outbox pattern para publicacao confiavel de eventos de confirmacao.
- Idempotency keys em comandos de agendamento e envio de notificacao.
- Classificacao de erro: transitorio, permanente, regra de negocio.

## 6) Modelo de dados logico (entidades principais e dados sensiveis)
### Entidades principais
- Paciente
  - Identificadores, contato, preferencia de comunicacao.
- PedidoMedico
  - Identificador, tipo exame, metadados do documento, status de validacao.
- UnidadeAtendimento
  - Identificador da unidade, localizacao, capacidades relevantes para oferta.
- SlotAgenda
  - Data/hora, recurso, status (disponivel, reservado, confirmado).
- Agendamento
  - Identificador, paciente, exame, unidade, horario, status, origem digital.
- Notificacao
  - Canal, payload resumido, status envio, tentativas.
- AuditoriaAcesso
  - Usuario/sistema, acao, timestamp, contexto e resultado.

### Dados sensiveis e classificacao
- Dados pessoais: identificacao e contato do paciente.
- Dados pessoais sensiveis: informacoes de saude presentes no pedido medico e contexto de exame.
- Dados operacionais sensiveis: trilhas de auditoria com contexto de acesso clinico.

### Regras de tratamento
- Minimizar campos obrigatorios na jornada inicial.
- Separar armazenamento de documento medico (blob seguro) e metadados transacionais.
- Criptografia em repouso para dados sensiveis e controle de acesso por papel/necessidade.

## 7) Requisitos nao funcionais (seguranca, desempenho, disponibilidade, observabilidade)
### Seguranca
- Autenticacao forte para canais internos e protecao de sessao no portal.
- Autorizacao por papel e escopo para operacoes administrativas.
- Criptografia TLS em transito e criptografia em repouso para dados sensiveis.
- Segregacao de segredos em cofre de segredos e rotacao planejada.

### Desempenho
- Resposta interativa adequada para busca de horarios e confirmacao de agendamento sob carga esperada.
- Caching tatico de dados estaveis (unidades, tipos de exame permitidos).
- Evitar bloqueios longos na jornada sincrona, deslocando notificacoes para processamento assincrono.

### Disponibilidade
- Arquitetura stateless na API para escalabilidade horizontal.
- Politicas de resiliencia para dependencias externas (retry, timeout, circuit breaker).
- Degradacao graciosa quando integracoes externas estiverem indisponiveis.

### Observabilidade
- Logging estruturado por evento de negocio e por integracao externa.
- Tracing distribuido com correlation-id.
- Dashboards de saude operacional e fila de pendencias.
- Alertas para falhas repetidas de integracao e acumulo de reprocessamento.

## 8) Controles LGPD/sigilo medico (base legal, minimizacao, retencao, trilha de auditoria, acesso)
### Base legal e finalidade
- Tratar dados estritamente para execucao da jornada de agendamento e obrigacoes assistenciais aplicaveis.
- Registrar finalidade por categoria de dado e operacao critica.

### Minimizacao e necessidade
- Coletar apenas dados necessarios para triagem, elegibilidade e confirmacao.
- Adiar coletas nao essenciais para etapas posteriores quando possivel.

### Retencao e descarte
- Definir politica formal de retencao por tipo de dado/documento conforme obrigacoes legais e assistenciais.
- Implementar descarte seguro e auditavel ao fim da janela de retencao.

### Trilha de auditoria
- Auditar acessos, alteracoes, envios de notificacao e tentativas de operacoes criticas.
- Garantir integridade de logs e restricao de acesso a trilhas.

### Controle de acesso
- RBAC com principio do menor privilegio.
- Segregacao entre perfis de operacao, suporte e administracao.
- Revisao periodica de acessos e revogacao tempestiva.

## 9) Decisoes arquiteturais (ADRs) com alternativas e trade-offs
### ADR-01: Estilo arquitetural principal
- Decisao: Modular Monolith (.NET) com Clean Architecture e Vertical Slices.
- Alternativas avaliadas:
  - Microsservicos desde o inicio.
  - Monolito em camadas sem modularizacao explicita.
- Trade-offs:
  - Pro: menor custo de coordenacao, deploy unico, velocidade para 3 meses.
  - Contra: risco de crescimento de acoplamento se governanca de modulos falhar.

### ADR-02: Integracao sincrona + assincorna
- Decisao: consultas criticas sincronas (disponibilidade/reserva) e confirmacoes assincronas por eventos.
- Alternativas avaliadas:
  - Totalmente sincrono.
  - Event-driven pleno para toda a jornada.
- Trade-offs:
  - Pro: UX direta no agendamento e resiliencia no envio de notificacao.
  - Contra: maior complexidade operacional que um fluxo 100% sincrono.

### ADR-03: Persistencia de documentos medicos
- Decisao: blob storage seguro para arquivo + banco relacional para metadados e status.
- Alternativas avaliadas:
  - Tudo em banco relacional.
  - Repositorio documental unico sem metadados estruturados.
- Trade-offs:
  - Pro: melhor custo/escala para arquivos e consulta eficiente por metadados.
  - Contra: exige governanca de consistencia entre blob e metadados.

### ADR-04: Resiliencia de integracoes externas
- Decisao: politicas de timeout, retry com backoff, circuit breaker e idempotencia em comandos.
- Alternativas avaliadas:
  - Retry simples sem classificacao de erro.
  - Sem idempotencia, confiando apenas no legado.
- Trade-offs:
  - Pro: reduz duplicidade e falhas intermitentes percebidas pelo usuario.
  - Contra: aumenta trabalho de desenho de contratos e observabilidade.

### ADR-05: Autenticacao/autorizacao
- Decisao: identidade centralizada para equipe interna e controles de sessao seguros para paciente.
- Alternativas avaliadas:
  - Credenciais locais na aplicacao.
  - Acesso anonimo estendido sem camadas adicionais de validacao.
- Trade-offs:
  - Pro: melhor postura de seguranca e trilha de acesso.
  - Contra: maior esforco inicial de configuracao.

## 10) Estrategia de entrega em 3 meses (fases, MVP, riscos e mitigacoes)
### Fase 1 - Fundacao e MVP tecnico
- Estruturar solucao .NET, modularizacao interna, pipeline CI/CD e baseline de seguranca/observabilidade.
- Implementar jornada MVP: triagem basica, upload de pedido, consulta de disponibilidade, selecao de horario e confirmacao.
- Integrar fluxo principal com RIS/HIS no caminho feliz e mensageria de confirmacao.

### Fase 2 - Robustez operacional e compliance
- Completar politicas de resiliencia, idempotencia e reprocessamento.
- Endurecer controles LGPD/sigilo, auditoria e governanca de acesso.
- Refinar tratamento de erros de negocio e experiencia de indisponibilidade.

### Fase 3 - Estabilizacao e prontidao de operacao
- Testes integrados ponta a ponta e testes de carga aderentes ao contexto.
- Preparar runbooks, paineis e alertas para operacao assistida.
- Handoff para sustentacao com backlog de melhorias pos-MVP.

### Riscos principais e mitigacoes
- Risco: dependencia de APIs HIS/RIS com comportamento variavel.
  - Mitigacao: contratos internos estaveis, mocks de integracao e testes de contrato continuos.
- Risco: equipe reduzida para backlog tecnico + funcional.
  - Mitigacao: foco em escopo in, priorizacao rigorosa e corte de escopo nao essencial.
- Risco: falhas de mensageria externa afetarem confirmacoes.
  - Mitigacao: fila de retries, painel de pendencias e procedimento operacional de contingencia.
- Risco: gaps de compliance tardios.
  - Mitigacao: privacy/security checkpoints por sprint com criterios de aceite objetivos.

## 11) SDD objetivo para handoff de implementacao
### 11.1 Visao de implementacao
- Plataforma alvo: .NET (API + workers), banco relacional para dados transacionais, blob seguro para documentos, mensageria para eventos de confirmacao.
- Estrutura sugerida de solucao:
  - src/WebPortal
  - src/Scheduling.Api
  - src/Scheduling.Application
  - src/Scheduling.Domain
  - src/Scheduling.Infrastructure
  - src/Scheduling.Workers
  - tests/Unit
  - tests/Integration

### 11.2 Contratos principais de API (alto nivel)
- POST /agendamentos/prevalidar
  - Entrada: dados basicos + contexto exame.
  - Saida: elegibilidade basica e proximos passos.
- POST /documentos/pedido-medico
  - Entrada: arquivo + metadados minimos.
  - Saida: identificador e status inicial de validacao.
- GET /disponibilidade
  - Entrada: unidade/tipo exame/janela.
  - Saida: slots disponiveis.
- POST /agendamentos
  - Entrada: paciente, exame, unidade, slot, chave de idempotencia.
  - Saida: agendamento confirmado ou erro de negocio.

### 11.3 Eventos e mensagens
- AgendamentoConfirmado
  - Consumidor: worker de notificacao.
- NotificacaoEnviada
  - Consumidor: trilha operacional/compliance.
- NotificacaoFalhou
  - Consumidor: fila de pendencias/reprocessamento.

### 11.4 Regras de implementacao obrigatorias
- Toda operacao de comando deve aceitar chave de idempotencia.
- Todo fluxo de integracao externa deve propagar correlation-id.
- Logs devem ser estruturados e sem exposicao de dado sensivel em texto claro.
- Excecoes de negocio devem retornar codigo e mensagem padronizados.

### 11.5 Testes e qualidade
- Unit tests para regras de dominio e validadores de comandos.
- Integration tests para adaptadores HIS/RIS/mensageria com cenarios de falha.
- Contract tests para APIs externas quando possivel.
- Testes de seguranca focados em autorizacao, upload seguro e trilha de auditoria.

### 11.6 Operacao e suporte
- Runbook para falha de integracao e backlog de retries.
- Procedimento de contingencia para indisponibilidade temporaria de RIS/HIS.
- Dashboard minimo com saude da API, workers e filas.

## 12) Checklist de prontidao para codificacao
- Escopo in/out validado e congelado para MVP.
- Fonte de verdade de disponibilidade (HIS ou RIS) formalmente definida.
- Contratos de integracao revisados com times proprietarios de HIS/RIS.
- Modelo de dados com classificacao de sensibilidade aprovado.
- Politicas de idempotencia, retries e timeouts documentadas.
- Regras LGPD/sigilo traduzidas em criterios de aceite tecnicos.
- Pipeline CI/CD com validacoes de qualidade e seguranca habilitado.
- Observabilidade minima pronta (logs estruturados, traces, alertas).
- Plano de testes unitarios/integracao/contrato aprovado.
- Runbooks operacionais e fluxo de incidentes definidos.

## Riscos residuais
- Dependencia de maturidade operacional dos sistemas legados para disponibilidade em tempo real.
- Possivel necessidade de ajuste de UX em casos de indisponibilidade recorrente de integracoes.
- Variacoes de processo entre unidades podem demandar regras adicionais apos MVP.

## Decisoes pendentes
- Definicao final da fonte de verdade primaria para disponibilidade em tempo real.
- Politica detalhada de retencao por categoria documental com validacao juridica/compliance.
- Regras de autenticacao do paciente (nivel de garantia exigido para primeiro release).
- Definicao da estrategia de contingencia operacional formal para janela de indisponibilidade prolongada de integracoes.
