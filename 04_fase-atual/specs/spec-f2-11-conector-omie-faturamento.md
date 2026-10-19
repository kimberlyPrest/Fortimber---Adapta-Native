# SPEC-F2-11 — Conector Omie (faturamento)

**Fase:** 2 — Onda 2 (conectores)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §7 (Omie: API acessível, já integrado à área de dados); §8 Fase 2; D-F1-02
**Degrau da solução:** reuso — usa a integração Omie que já alimenta a área de dados e acrescenta um tipo de evento ao modelo existente.

## Resultado observável

Os faturamentos do Omie aparecem no detalhe do contrato como eventos `faturamento`, ligados ao item, com o número da nota como registro de origem. Eles só contam como entrega se a decisão D-F1-02 disser que sim; caso contrário, servem para reconciliação. Faturamento sem vínculo seguro nunca é associado por aproximação: vai para a fila de divergências.

## Limites e dependências

- **Inclui:**
  - inventário dos endpoints Omie já consumidos (entidades, limites de taxa, paginação, campos) e da chave pedido Omie ↔ contrato;
  - `faturamento` como novo valor de `evento_tipo`;
  - parâmetro de regra "faturamento conta como entrega" (sim/não), definido pela D-F1-02 e lido pelo cálculo de saldo;
  - job que envia os eventos pela API de ingestão;
  - reconciliação por amostra com o Financeiro.
- **Fora de escopo:** valores financeiros como indicador e margem (o valor da nota pode ser guardado, sem KPI); escrita no Omie; dados de pessoas físicas além do nome do cliente já presente no contrato.
- **Entradas e pré-condições:** saldo multifonte (SPEC-F2-01); motor e API (SPECs F2-08 e F2-09); D-F1-02 (SPEC-F2-04).
- **Saídas/artefatos:** `03_documentos/integracao/inventario-omie.md`; migração do tipo de evento; job; planilha de reconciliação.
- **Dependências e responsáveis:** Champion; Financeiro valida.
- **Risco e plano B:** se não houver chave pedido ↔ contrato confiável, fazer o vínculo por tabela de correspondência mantida pelo Comercial, nunca por semelhança de nome ou valor.
- **Rollback ou reversão:** reverter as execuções do Omie; o parâmetro de contagem volta a "não".

## Fluxo e regras

1. O job lê os faturamentos do período desde a última execução bem-sucedida.
2. Para cada nota, resolve o contrato e o item pela chave documentada.
3. Com vínculo: envia evento `faturamento` com `fonte = omie` e `registro_origem` = número e série da nota.
4. Sem vínculo: envia para a fila como `evento_sem_vinculo` (SPEC-F2-12).
5. O saldo inclui `faturamento` apenas se o parâmetro da D-F1-02 estiver ligado.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Nota com pedido vinculado | Evento no item, com nota e data | — |
| Limite | Nota sem pedido vinculado | Divergência `evento_sem_vinculo` | Comercial vincula na tabela de correspondência |
| Regra | D-F1-02 = faturamento não conta | Saldo inalterado; faturamento visível | — |
| Falha | Limite de taxa do Omie | Espera e nova tentativa; execução marcada se esgotar | Reexecutar |

## Checklist de execução

- [ ] Inventário publicado.
- [ ] Tipo `faturamento` e parâmetro de regra criados.
- [ ] Job com paginação, limite de taxa e novas tentativas.
- [ ] Notas sem vínculo indo para a fila.
- [ ] Reconciliação com o Financeiro.

## Critérios de aceite

- [ ] **CA-F2-11-1:** inventário publicado, com endpoints, limites, chave e campos usados.
- [ ] **CA-F2-11-2:** faturamento com chave válida aparece no item, com a nota como registro de origem.
- [ ] **CA-F2-11-3:** faturamento sem chave não é vinculado e aparece na fila como `evento_sem_vinculo`.
- [ ] **CA-F2-11-4:** com o parâmetro "não", o saldo não muda; com "sim", desconta uma única vez, junto com as demais fontes (CA-F2-01-1).
- [ ] **CA-F2-11-5:** reexecutar o mesmo período não duplica eventos.
- [ ] **CA-F2-11-6:** para 20 contratos da amostra, a quantidade faturada no sistema é igual à do Omie, em planilha assinada pelo Financeiro.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Procurar eventos com `fonte = omie` | Nenhum | Consulta |
| GREEN | CA-F2-11-2 a -5 | Executar o job para um período de homologação com notas vinculadas e sem vínculo; alternar o parâmetro | Conforme os critérios | Execuções e capturas |
| REFACTOR/REGRESSÃO | CA-F2-11-1 e -6 | Revisar o inventário; reconciliar com o Financeiro | Planilha assinada | Documento e planilha |

**Dados/fixtures:** período de homologação acordado com o Financeiro.
**Caminhos de erro obrigatórios:** nota cancelada, nota sem pedido, limite de taxa, unidade diferente da do contrato.
**Evidência exigida:** relatórios de execução, capturas e planilha de reconciliação.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T36 | Inventariar os endpoints Omie consumidos e a chave pedido ↔ contrato | Fábio Pimentel (Champion) | SPEC-F2-11 | Endpoints, limites, paginação, campos e chave documentados. | CA-F2-11-1 | A fazer |
| F2-T37 | Adicionar o evento `faturamento` e o parâmetro de contagem definido pela D-F1-02 | Fábio Pimentel (Champion) | SPEC-F2-11 | Tipo novo aceito pelo modelo.<br>Saldo respeita o parâmetro. | CA-F2-11-4 | A fazer |
| F2-T38 | Construir o job Omie que envia faturamentos vinculados e manda os sem vínculo para a fila | Fábio Pimentel (Champion) | SPEC-F2-11 | Nota vinculada aparece no item.<br>Sem vínculo vira divergência.<br>Reexecução não duplica. | CA-F2-11-2, -3 e -5 | A fazer |
| F2-T39 | Reconciliar a quantidade faturada de 20 contratos entre o sistema e o Omie | Financeiro Fortimber | SPEC-F2-11 | Planilha assinada.<br>Diferenças classificadas. | CA-F2-11-6 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
