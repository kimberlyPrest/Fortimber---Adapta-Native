# SPEC-F2-08 — Sincronização versionada e repetível

**Fase:** 2 — Onda 1 (fundação de integração)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §8 Fase 2 ("reprocessamento idempotente e histórico de carga", "registro de execução por fonte"); §6 regra 1 (aditivos preservam histórico)
**Degrau da solução:** reuso e construção mínima — evolui a lógica de `cargas_publicar.js` e `cargas_reverter.js` para um motor único, usado pela carga manual e pela ingestão (SPEC-F2-09); coleções novas `execucoes_fonte` e `versoes_registro`.

## Resultado observável

Qualquer fonte pode ser sincronizada quantas vezes for preciso:
- reexecutar o mesmo lote não muda nada;
- contrato ou item existente com dado novo é atualizado e a versão anterior fica consultável;
- aditivo vira nova revisão, com histórico;
- cada execução tem relatório próprio;
- reverter uma execução volta exatamente ao estado anterior.

## Limites e dependências

- **Inclui:**
  1. Identidade do contrato por (`sistema_origem`, `id_externo`). Hoje a busca usa só `id_externo`, o que gera falsas colisões entre sistemas.
  2. Revisão maior que a vigente: cria a nova revisão e marca a anterior como substituída; itens copiados e atualizados.
  3. Revisão menor ou igual com conteúdo diferente: não sobrescreve; gera divergência `revisao_retroativa` (SPEC-F2-12).
  4. Mesma revisão com campo alterado (quantidade, unidade, estado, prazo, preço): grava o antes e o depois em `versoes_registro` e atualiza o registro; o saldo é recalculado.
  5. Evento pode apontar para contrato já existente fora do lote. Hoje é exigido que o contrato esteja no mesmo arquivo.
  6. `execucoes_fonte`: `fonte`, `iniciado_em`, `finalizado_em`, `estado` (em_andamento, sucesso, sucesso_com_excecoes, falha, revertida), `lidos`, `criados`, `alterados`, `inalterados`, `rejeitados`, `erros`, `checksum`, `extraido_em` (informado pela fonte) e `disparado_por`.
  7. Rollback por execução: remove os eventos criados, restaura as versões anteriores, remove os contratos criados e recalcula o saldo. Só é permitido se nenhuma execução posterior da mesma fonte tocou os mesmos registros.
  8. Tudo dentro de uma transação: falha não altera o estado publicado.
- **Fora de escopo:** conectores (SPECs F2-10 e F2-11); resolução de divergências (SPEC-F2-12); sincronização bidirecional.
- **Entradas e pré-condições:** modelo canônico da Fase 1; regras da SPEC-F2-01; decisão D-F1-03 sobre aditivos (sem ela, vale a premissa: aditivo substitui a quantidade vigente).
- **Saídas/artefatos:** coleções e migrações novas; motor reutilizável; tela de Cargas usando o motor; histórico de versões no detalhe do contrato.
- **Dependências e responsáveis:** Champion.
- **Risco e plano B:** volume de cerca de 4.500 contratos por execução. Se a transação única ficar lenta, processar em blocos por contrato, com a execução marcada `sucesso_com_excecoes` em caso de falha parcial e rollback por bloco.
- **Rollback ou reversão:** o próprio rollback por execução; as migrações têm `down`.

## Fluxo e regras

1. Abrir uma execução (`em_andamento`) com checksum e `extraido_em`.
2. Se o checksum for igual ao da última execução bem-sucedida da mesma fonte, encerrar como `sucesso` com 0 alterações.
3. Para cada contrato e item: criar, versionar ou manter, conforme as regras 2 a 4.
4. Para cada evento: deduplicar (regra atual) e vincular a contrato e item existentes.
5. Fechar a execução com contagens e erros.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Mesmo lote enviado duas vezes | Segunda execução: 0 criados, 0 alterados | — |
| Atualização | Quantidade de 100 para 120 no mesmo contrato e revisão | Item com 120; versão 100 no histórico; saldo recalculado | Rollback volta para 100 |
| Aditivo | Revisão 2 do contrato | Revisão 2 vigente; revisão 1 no histórico | — |
| Falha | Erro no meio do lote | Nada alterado; execução `falha` com erro | Corrigir e reenviar |
| Bloqueio | Reverter execução antiga já sobreposta | Recusa com a execução conflitante indicada | Reverter na ordem inversa |

## Checklist de execução

- [ ] Busca por (`sistema_origem`, `id_externo`).
- [ ] Versionamento de campo e de revisão.
- [ ] Eventos para contratos existentes fora do lote.
- [ ] `execucoes_fonte` preenchida em toda execução.
- [ ] Rollback por execução com restauração de versão.
- [ ] Carga manual (tela Cargas) usando o motor.
- [ ] Histórico de versões visível no detalhe do contrato.

## Critérios de aceite

- [ ] **CA-F2-08-1:** reexecutar o mesmo lote resulta em 0 criados, 0 alterados e N inalterados.
- [ ] **CA-F2-08-2:** lote com quantidade alterada para contrato existente atualiza o item, mostra a versão anterior no detalhe e recalcula o saldo.
- [ ] **CA-F2-08-3:** aditivo (revisão 2) deixa a revisão 2 vigente e a revisão 1 consultável.
- [ ] **CA-F2-08-4:** evento de contrato já existente e não incluído no lote é aceito e vinculado.
- [ ] **CA-F2-08-5:** reverter a execução do CA-F2-08-2 devolve a quantidade anterior, remove os eventos da execução e recalcula o saldo.
- [ ] **CA-F2-08-6:** falha no meio do lote não altera nenhum registro e marca a execução como `falha`, com o erro.
- [ ] **CA-F2-08-7:** a carga CSV pela tela de Cargas usa o motor e passa nos CA-F2-08-1 a -6.
- [ ] **CA-F2-08-8:** os cenários de carga da Fase 1 (duplicidade, carga inválida) mantêm o resultado.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Atualização no motor atual | Publicar uma carga e depois outra com a quantidade alterada | Quantidade não muda (falha reproduzida) | Captura do detalhe |
| GREEN | CA-F2-08-1 a -6 | Sequência de lotes `L1` (base), `L1` de novo, `L2` (alteração), `L3` (aditivo), `L4` (evento avulso), `L5` (com erro) e reversão de `L2` | Conforme os critérios | Relatórios das execuções |
| REFACTOR/REGRESSÃO | CA-F2-08-7 e -8 | Repetir pela tela de Cargas; rodar os cenários de carga da Fase 1 | Sem regressão | Capturas |

**Dados/fixtures:** lotes `L1`–`L5` com 3 contratos fictícios, preparados pelo Champion.
**Caminhos de erro obrigatórios:** colisão entre sistemas, revisão retroativa, rollback fora de ordem, lote vazio, lote acima do limite.
**Evidência exigida:** relatórios de execução e capturas do histórico.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T24 | Criar `execucoes_fonte` e registrar toda carga com contagens, checksum e `extraido_em` | Fábio Pimentel (Champion) | SPEC-F2-08 | Execução aberta e fechada com contagens e erros.<br>Mesmo checksum encerra com 0 alterações. | CA-F2-08-1 e CA-F2-08-6 (registro da falha) | A fazer |
| F2-T25 | Implementar o upsert por (`sistema_origem`, `id_externo`) com revisões e eventos para contratos já existentes | Fábio Pimentel (Champion) | SPEC-F2-08 | Aditivo cria revisão e preserva a anterior.<br>Revisão retroativa não sobrescreve.<br>Evento avulso é vinculado. | CA-F2-08-3 e CA-F2-08-4 | A fazer |
| F2-T26 | Versionar alterações de campo em contrato e item e recalcular o saldo | Fábio Pimentel (Champion) | SPEC-F2-08 | Antes e depois gravados em `versoes_registro`.<br>Histórico visível no detalhe.<br>Saldo recalculado. | CA-F2-08-2 | A fazer |
| F2-T27 | Implementar o rollback por execução com restauração de versões e bloqueio fora de ordem | Fábio Pimentel (Champion) | SPEC-F2-08 | Reversão restaura valores e remove eventos da execução.<br>Recusa se houver execução posterior conflitante. | CA-F2-08-5 e cenário Bloqueio | A fazer |
| F2-T28 | Migrar a carga CSV da tela de Cargas para o motor versionado | Fábio Pimentel (Champion) | SPEC-F2-08 | Tela de Cargas usa o motor.<br>Cenários de carga da Fase 1 sem regressão. | CA-F2-08-7 e CA-F2-08-8 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
