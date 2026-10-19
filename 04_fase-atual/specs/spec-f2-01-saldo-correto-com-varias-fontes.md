# SPEC-F2-01 — Saldo correto com várias fontes

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §6 regras 1 e 5; §8 Fase 2 (reconciliação)
**Degrau da solução:** reuso — altera o hook existente `pocketbase/hooks/on_evento_saldo.js`; nenhuma dependência nova.

## Resultado observável

O saldo de um item nunca desconta duas vezes a mesma entrega quando duas ou mais fontes a reportam. Quando as fontes discordam, o item aparece como "em divergência", com o total de cada fonte visível no detalhe do contrato. O saldo é recalculado sempre que um evento é criado, alterado ou removido. Quando existir fonte preferencial aprovada (SPEC-F2-07), é ela que define o saldo e a diferença das demais continua visível.

## Limites e dependências

- **Inclui:** regra de cálculo multifonte; recálculo em criação, alteração e remoção de evento; resumo por fonte no detalhe; uso da preferência vigente quando houver.
- **Fora de escopo:** escolher sozinho qual fonte está certa; tolerância numérica (igualdade exata até D-F1-06 aprovar tolerância); conversão de unidades; alteração de quantidade contratada (SPEC-F2-08).
- **Entradas e pré-condições:** coleções `contratos_itens` e `contratos_eventos` atuais; casos 1–8 da SPEC-F1-01 §10 como regressão.
- **Saídas/artefatos:** campos novos em `contratos_itens`: `fontes_resumo` (json `{fonte: total}`) e `divergencia_fontes` (booleano); hook revisado; bloco "Entregue por fonte" em `ContratoDetalhe.tsx`.
- **Dependências e responsáveis:** preferência por campo vem de SPEC-F2-07 (Champion); a matriz de preferência é decidida em SPEC-F2-04 (D-F1-06, Comercial).
- **Risco e plano B:** se D-F1-06 atrasar, vale a regra provisória (fontes iguais contam uma vez; diferentes ficam em divergência). Ela é segura porque nunca inventa saldo.
- **Rollback ou reversão:** reverter o hook para a versão anterior e recalcular os itens; os campos novos são aditivos e podem ficar sem uso.

## Fluxo e regras

1. Ao criar, alterar ou remover um evento, recalcular o item afetado (e o item anterior, se o evento mudou de item).
2. Considerar só os tipos que contam como entrega pela regra vigente (D-F1-02): hoje `expedicao`, `entrega_confirmada` e `devolucao`; `producao` e `faturamento` (SPEC-F2-11) não contam, salvo decisão.
3. Se algum evento tiver quantidade ausente ou unidade diferente da do item, o item fica `nao_calculavel` (regra atual mantida).
4. Somar a entrega **por fonte** e gravar `fontes_resumo`.
5. Uma fonte só: saldo = quantidade vigente − total dessa fonte.
6. Duas ou mais fontes **com** preferência vigente para "quantidade entregue": saldo = vigente − total da preferencial; `divergencia_fontes = true` se alguma outra fonte tiver total diferente.
7. Duas ou mais fontes **sem** preferência: se todos os totais forem iguais, saldo = vigente − esse total (contado uma vez); se diferentes, `saldo_estado = em_divergencia` e saldo vazio.
8. Nulo nunca vira zero; excedente aparece negativo (D-F1-03, premissa atual).

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Vigente 100; Excel 50 e Omie 50 para a mesma entrega; sem preferência | Saldo 50; resumo Excel 50 / Omie 50; sem divergência | — |
| Limite | Excel 50, Omie 30; sem preferência | `em_divergencia`; saldo vazio; resumo 50/30 visível | Resolver pela fila (SPEC-F2-12) ou definir preferência |
| Preferência | Mesmo caso, Omie preferencial | Saldo 70; aviso "divergência com Excel (50)" | Trocar preferência gera nova vigência |
| Remoção | Evento de 50 removido | Saldo recalculado na mesma operação | Falha no recálculo fica no log, com item e erro |
| Falha | Evento com unidade "pc" num item em m³ | `nao_calculavel` com motivo | Corrigir na origem e sincronizar |

## Checklist de execução

- [ ] Hook recalcula em criação, alteração e remoção de evento.
- [ ] Campos `fontes_resumo` e `divergencia_fontes` criados por migração.
- [ ] Detalhe do contrato exibe a entrega por fonte e o estado de concordância.
- [ ] Preferência vigente (SPEC-F2-07) aplicada quando existir.
- [ ] Casos 1–8 da SPEC-F1-01 §10 conferidos sem mudança de resultado.

## Critérios de aceite

- [ ] **CA-F2-01-1:** vigente 100 com Excel 50 e Omie 50 para a mesma entrega, sem preferência → saldo 50 e as duas fontes listadas como concordantes.
- [ ] **CA-F2-01-2:** Excel 50 e Omie 30, sem preferência → `em_divergencia`, saldo vazio, totais 50 e 30 visíveis no detalhe.
- [ ] **CA-F2-01-3:** alterar ou remover um evento recalcula o saldo do item na mesma operação, sem nova carga.
- [ ] **CA-F2-01-4:** com Omie como fonte preferencial vigente → saldo 70 e aviso de divergência com o Excel visível.
- [ ] **CA-F2-01-5:** os casos 1–8 da SPEC-F1-01 §10 mantêm o resultado esperado.

## TDD da SPEC

Prova por cenário demonstrado na aplicação. O time Fortimber faz testes automatizados, mas o registro deles não é exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Cenário principal no sistema atual | Carregar a fixture do CA-F2-01-1 antes da mudança | Saldo 0 (dupla contagem reproduzida) | Captura do detalhe |
| GREEN | CA-F2-01-1, -2 e -3 | Carregar as fixtures; alterar e remover um evento pelo painel admin | Saldos 50, divergência e recálculo imediato | Capturas do detalhe |
| REFACTOR/REGRESSÃO | CA-F2-01-4 e -5 | Configurar a preferência (após SPEC-F2-07); rodar os casos 1–8 | Saldo 70 com aviso; casos 1–8 inalterados | Tabela esperado × observado |

**Dados/fixtures:** contrato fictício `FX-MULTI-01` (100 m³) com eventos de `excel-controle` e `omie`.
**Caminhos de erro obrigatórios:** quantidade ausente, unidade incompatível, evento movido de item, remoção do último evento.
**Evidência exigida:** capturas do detalhe e tabela esperado × observado anexadas à task.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T01 | Reescrever o cálculo de saldo com total por fonte e recálculo em criação, alteração e remoção de evento | Fábio Pimentel (Champion) | SPEC-F2-01 | Hook recalcula nos três gatilhos.<br>Fontes iguais contam uma vez; diferentes viram `em_divergencia`.<br>Campos `fontes_resumo` e `divergencia_fontes` gravados. | CA-F2-01-1, -2, -3 e -5 (GREEN e REGRESSÃO) | A fazer |
| F2-T02 | Exibir no detalhe do contrato a entrega por fonte e o estado de concordância | Fábio Pimentel (Champion) | SPEC-F2-01 | Bloco 'Entregue por fonte' com total de cada fonte.<br>Indicação concorda/diverge visível. | Captura do CA-F2-01-2 (totais 50/30 visíveis) | A fazer |
| F2-T03 | Aplicar a fonte preferencial vigente (e a preferência pontual por item) no cálculo do saldo | Fábio Pimentel (Champion) | SPEC-F2-01 | Com preferência vigente, saldo segue a preferencial.<br>Aviso de divergência com as demais fontes. | CA-F2-01-4 (REFACTOR/REGRESSÃO) | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
