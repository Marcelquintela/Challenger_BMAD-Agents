# AS-IS Discovery Arquitetural - Canal Digital de Agendamento de Exames

## 1) Resumo executivo (5-8 linhas)
O cenário atual de agendamento de exames é fortemente dependente de telefone e mensagens, gerando alto tempo de espera e pressão operacional na equipe.
Essa dinâmica contribui para abandono no processo de agendamento e para janelas ociosas causadas por cancelamentos de última hora sem reposição rápida.
O objetivo de negócio declarado é estabelecer um canal digital 24x7 para ampliar ocupação de agendas, melhorar retorno de equipamentos de alto custo e aumentar conveniência/fidelização do paciente.
O escopo funcional previsto para evolução inclui portal web responsivo, triagem básica, upload de pedido médico, escolha de unidade/horário e confirmação automática.
As integrações prioritárias já apontadas são com sistema de gestão hospitalar e sistema de informações radiológicas, via APIs para disponibilidade em tempo real e mensageria externa para alertas.
O contexto impõe restrições relevantes: prazo de 3 meses, equipe reduzida e exigências de LGPD/sigilo médico.
Os critérios de sucesso focam migração de agendamentos para digital, redução do tempo de espera telefônico e maior precisão na validação de documentos.

## 2) Escopo do AS-IS (in/out)
### In (considerado neste AS-IS)
- Situação operacional atual de agendamento com canais predominantes telefone/mensagens.
- Dores observadas: espera, fadiga operacional, abandono e ociosidade por cancelamentos de última hora.
- Sistemas e integrações citados no caso: sistema de gestão hospitalar, sistema de informações radiológicas, APIs e gateway de mensageria.
- Restrições e critérios de sucesso explicitados no caso.
- Artefatos já disponíveis: fluxos atuais detalhados, documentação técnica de APIs e diretrizes visuais da marca.

### Out (fora do AS-IS atual ou fora do escopo funcional declarado)
- Pagamento antecipado.
- Reagendamentos complexos.
- Marcação de procedimentos com orientação clínica muito específica/preparos críticos monitorados.
- Definição de arquitetura TO-BE detalhada (fica para o Arquiteto).
- Estimativas numéricas não fornecidas no caso.

## 3) Atores e sistemas envolvidos
### Atores de negócio/operação
- Paciente (solicitante do agendamento).
- Equipe de atendimento/agendamento (operação atual por telefone/mensagens).
- Equipe assistencial/administrativa de apoio à validação documental (papel inferido; depende de validação).

### Sistemas e plataformas
- Sistema de gestão hospitalar (prontuários).
- Sistema de informações radiológicas (agendas de imagem).
- Gateways de mensageria externa para alertas/confirmacões.
- Canais atuais de contato por telefone e mensagens.

## 4) Fluxo atual ponta a ponta (passo a passo)
1. Paciente inicia contato para agendamento por telefone ou mensagens.
2. Paciente entra em fila de atendimento e aguarda retorno humano.
3. Equipe coleta informações básicas do exame e dados necessários para prosseguir.
4. Equipe consulta disponibilidade de agenda em sistemas internos (radiologia/gestão), com suporte operacional manual.
5. Equipe apresenta opções de unidade e horário ao paciente pelo mesmo canal de contato.
6. Paciente confirma opção; equipe registra o agendamento nos sistemas pertinentes.
7. Paciente envia ou apresenta pedido médico/documentos pelo canal disponível (envio digital por mensagem ou validação posterior, conforme prática local).
8. Confirmações e lembretes ocorrem via contato operacional (telefone/mensagens), sem evidência no caso de automação ponta a ponta já implantada.
9. Em cancelamentos de última hora, a reposição da vaga depende de ação rápida da equipe, frequentemente sem preenchimento tempestivo.

## 5) Integrações e dependências
- Dependência crítica de integração com sistema de gestão hospitalar para contexto de prontuário e dados administrativos relacionados.
- Dependência crítica de integração com sistema de informações radiológicas para disponibilidade de agendas de imagem.
- Prioridade declarada de APIs para disponibilidade em tempo real (fato), com impacto direto na confiabilidade de oferta de horários.
- Dependência de gateways de mensageria externa para alertas e confirmação de comunicação com paciente.
- Dependência de documentação técnica já disponível das APIs do sistema de gestão hospitalar para acelerar desenho de integração.
- Dependência organizacional de equipe multidisciplinar reduzida para execução e sustentação no prazo.

## 6) Dores e impactos (com causa -> efeito)
- Dependência de canais humanos (telefone/mensagens) -> filas de atendimento e alto tempo de espera.
- Alto tempo de espera -> abandono no agendamento e potencial perda de conversão.
- Operação concentrada em equipe reduzida -> fadiga operacional e maior risco de erros/retrabalho.
- Cancelamentos de última hora sem mecanismo ágil de reposição -> janelas ociosas na agenda.
- Janelas ociosas em agendas de exames -> menor retorno de equipamentos de alto custo.
- Validação documental não plenamente padronizada/automatizada no fluxo atual (inferência) -> retrabalho na chegada do paciente.

## 7) Riscos atuais (operacionais, técnicos, compliance)
### Operacionais
- Gargalo de capacidade humana em picos de demanda.
- Variabilidade no atendimento e no tempo de resposta por canal não estruturado.
- Risco de continuidade da ociosidade por baixa velocidade de recomposição de agenda.

### Técnicos
- Risco de inconsistência de disponibilidade sem integração em tempo real entre canais e agendas.
- Risco de acoplamento elevado a sistemas legados (gestão hospitalar e radiologia), afetando prazo de entrega.
- Risco de falhas de mensageria externa impactarem confirmações/alertas.

### Compliance (LGPD e sigilo médico)
- Risco de exposição de dados sensíveis em canais de mensagem sem governança adequada.
- Risco de coleta/armazenamento de documentos médicos sem trilha de consentimento e controles de acesso suficientes.
- Risco de não conformidade em retenção, auditoria e minimização de dados, se não houver desenho explícito de privacidade.

## 8) Fatos confirmados vs inferências
### Fatos confirmados (explicitamente informados)
- Objetivo de negócio: canal digital 24x7 para ocupação de agendas, retorno de equipamentos e conveniência/fidelização.
- Problemas atuais: dependência de telefone/mensagens, espera alta, fadiga da equipe, abandono e ociosidade por cancelamentos.
- Escopo in: portal responsivo, triagem básica, upload de pedido, seleção de unidade/horário, confirmação automática.
- Escopo out: pagamento antecipado, reagendamentos complexos, procedimentos com orientação clínica muito específica/preparos críticos monitorados.
- Sistemas e integrações: sistema de gestão hospitalar, sistema de informações radiológicas, APIs prioritárias para tempo real e gateways de mensageria externa.
- Restrições: prazo 3 meses, equipe reduzida, segurança/escalabilidade, LGPD e sigilo médico.
- Critérios de sucesso: migração para digital, redução de espera telefônica, validação mais precisa de documentos com menor retrabalho.
- Artefatos disponíveis: fluxos atuais detalhados, documentação técnica de APIs de gestão, diretrizes visuais.

### Inferências (derivadas, requerem validação)
- Parte da validação documental pode ocorrer de forma distribuída entre equipe de atendimento e apoio administrativo/assistencial.
- A reposição de vagas canceladas ocorre majoritariamente por esforço manual reativo.
- Não há automação ponta a ponta suficiente para confirmação e orquestração de fila no estado atual.
- Há variabilidade de processo entre unidades/canais, elevando risco de inconsistência operacional.

## 9) Lacunas e perguntas para validação humana
- Quais etapas exatas do fluxo atual já estão mapeadas no artefato de processos e qual versão é a fonte oficial?
- Quais canais de mensagem são oficialmente usados hoje e com quais políticas de segurança/auditoria?
- Quem é o responsável final pela validação do pedido médico e em que momento do fluxo essa validação ocorre?
- Quais dados mínimos são obrigatórios para triagem básica por tipo de exame?
- A disponibilidade em tempo real será obtida de qual sistema como fonte de verdade principal (gestão hospitalar ou radiologia)?
- Existe SLA operacional atual para contato inicial, confirmação e tratamento de cancelamentos?
- Quais eventos de mensageria são mandatórios (confirmação, lembrete, aviso de pendência documental, no-show)?
- Quais requisitos não funcionais já mandatórios (picos de acesso, observabilidade, rastreabilidade, trilha de auditoria)?
- Quais controles LGPD já existentes (base legal, consentimento, retenção, anonimização, direito de acesso/exclusão)?
- Há diferenças relevantes de processo por unidade física ou por tipo de exame?

## 10) Critérios de prontidão para handoff ao Arquiteto
- Fluxo AS-IS validado por operação com responsáveis nomeados por etapa.
- Matriz de atores, sistemas e responsabilidades (RACI simplificado) confirmada.
- Catálogo de integrações com origem/destino, eventos, payload mínimo e regras de erro documentado.
- Fonte de verdade para disponibilidade de agenda definida e validada entre áreas.
- Requisitos de compliance LGPD/sigilo traduzidos em controles verificáveis (acesso, criptografia, auditoria, retenção).
- Critérios de sucesso convertidos em métricas operacionais mensuráveis (sem impor metas numéricas não acordadas).
- Premissas e inferências revisadas e assinadas pelos donos de processo.
- Backlog de dúvidas críticas resolvido ou explicitamente aceito com plano de mitigação.
