# SPEC-F2-13 — Saúde das fontes e frescor visível

**Fase:** 2 — Onda 3 (reconciliação e visibilidade)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §6 regra 4 ("toda visão mostra 'atualizado em' por fonte"); §8 Fase 2 ("estado e horário por fonte aparecem; falha gera sinal visível")
**Degrau da solução:** reuso — lê `execucoes_fonte` e `fontes` e reaproveita o sino de notificações já existente no cabeçalho.

## Resultado observável

Uma tela mostra, para cada fonte: último sucesso, última falha, contagens da última execução e se está atrasada em relação à frequência acordada. O detalhe do contrato separa "extraído da fonte em" de "carregado no sistema em" e avisa quando a fonte está atrasada. Falha de execução gera aviso no sino para operadores e admins.

## Limites e dependências

- **Inclui:**
  - página `/saude-fontes`;
  - regra de atraso: atrasada quando agora − último sucesso > 1,5 × frequência acordada. É uma premissa, a ser aprovada pelo Comercial, e substitui o limite fixo de 7 dias em `ContratoDetalhe.tsx`;
  - "extraído em" vindo de `execucoes_fonte.extraido_em`, no lugar de `updated` do registro;
  - avisos no sino a partir das execuções com `falha` ou `sucesso_com_excecoes` e das fontes atrasadas;
  - fontes planejadas ou bloqueadas aparecem como lacuna, nunca como zero.
- **Fora de escopo:** alertas de prazo de contrato e de risco de entrega (Fases 3 e 4); envio de e-mail ou WhatsApp; monitoramento de infraestrutura.
- **Entradas e pré-condições:** `fontes` (SPEC-F2-07); `execucoes_fonte` (SPEC-F2-08); sino sem mocks (SPEC-F2-02).
- **Saídas/artefatos:** página; ajuste de `ContratoDetalhe.tsx`, `Contratos.tsx` e `services/contratos.ts`; `NotificationsPopover.tsx` ligado a dados reais.
- **Dependências e responsáveis:** Champion; Comercial aprova a regra de atraso.
- **Risco e plano B:** fonte sem frequência acordada aparece como "frequência não definida", sem cálculo de atraso.
- **Rollback ou reversão:** reverter o commit; não há migração destrutiva.

## Fluxo e regras

1. A tela calcula o estado de cada fonte: ok, atrasada, com falha, lacuna ou sem frequência definida.
2. O detalhe do contrato busca a execução que gravou o dado e mostra as duas datas.
3. O sino mostra os avisos não lidos para operador e admin; o leitor vê apenas o aviso de fonte atrasada no próprio contrato.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Fonte com sucesso recente | Estado "ok", com contagens | — |
| Atraso | Último sucesso além do limite | "Atrasada" na tela e aviso no detalhe dos contratos daquela fonte | Executar o job |
| Falha | Job falha | "Com falha" e aviso no sino em até 1 ciclo | Corrigir e reexecutar |
| Lacuna | Fonte planejada | "Lacuna — não integrada" | — |

## Checklist de execução

- [ ] Página de saúde das fontes.
- [ ] Regra de atraso parametrizada pela frequência.
- [ ] Datas de extração e de carga separadas no detalhe.
- [ ] Sino ligado a falhas reais.
- [ ] Lacunas exibidas.

## Critérios de aceite

- [ ] **CA-F2-13-1:** a tela lista cada fonte integrada com último sucesso, última falha e contagens.
- [ ] **CA-F2-13-2:** ao forçar falha do job, a fonte aparece "com falha" e o sino avisa operadores em até 1 ciclo da frequência.
- [ ] **CA-F2-13-3:** fonte sem sucesso além do limite aparece "atrasada" na tela e com aviso no detalhe dos contratos daquela fonte.
- [ ] **CA-F2-13-4:** o detalhe do contrato mostra "extraído da fonte em" e "carregado no sistema em" separados.
- [ ] **CA-F2-13-5:** fonte planejada ou bloqueada aparece como lacuna, não como zero.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Abrir o detalhe de um contrato | Uma data só, vinda de `updated`; limite fixo de 7 dias | Captura |
| GREEN | CA-F2-13-1 a -4 | Execução ok; execução com falha; fonte com frequência curta para simular atraso | Conforme os critérios | Capturas da tela e do sino |
| REFACTOR/REGRESSÃO | CA-F2-13-5 | Conferir as fontes planejadas | Lacuna exibida | Captura |

**Dados/fixtures:** execuções de homologação das SPECs F2-10 e F2-11.
**Caminhos de erro obrigatórios:** fonte sem execução, frequência indefinida, execução em andamento há muito tempo.
**Evidência exigida:** capturas.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T43 | Construir a tela de saúde das fontes com estado, último sucesso e falha, contagens e atraso parametrizado | Fábio Pimentel (Champion) | SPEC-F2-13 | Estados ok, atrasada, com falha, lacuna e sem frequência.<br>Regra de atraso pela frequência acordada. | CA-F2-13-1, -3 (tela) e -5 | A fazer |
| F2-T44 | Separar 'extraído da fonte em' de 'carregado em' no contrato e avisar fonte atrasada | Fábio Pimentel (Champion) | SPEC-F2-13 | Duas datas no detalhe.<br>Limite fixo de 7 dias removido.<br>Aviso nos contratos da fonte atrasada. | CA-F2-13-3 (detalhe) e CA-F2-13-4 | A fazer |
| F2-T45 | Ligar o sino a falhas reais de execução e a fontes atrasadas | Fábio Pimentel (Champion) | SPEC-F2-13 | Falha aparece no sino de operador e admin em até 1 ciclo. | CA-F2-13-2 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
