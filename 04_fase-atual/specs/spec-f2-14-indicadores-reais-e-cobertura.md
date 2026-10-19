# SPEC-F2-14 — Visão Geral com indicadores reais e cobertura por fonte

**Fase:** 2 — Onda 3 (reconciliação e visibilidade)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §2 (indicadores iniciais acordados); §8 critério global 1 (cobertura de contratos ativos); §10 (indicadores e medição); §8 Fase 2 ("fonte ainda não integrada permanece identificada como lacuna e não contamina métricas")
**Degrau da solução:** reuso — evolui a Visão Geral provisória (SPEC-F2-02) e o componente `KpiCard`.

## Resultado observável

A Visão Geral mostra:
- (a) contratos ativos com saldo, como n de N;
- (b) contratado × entregue por unidade e por período, sem misturar unidades;
- (c) cobertura por fonte e as lacunas.

Cada indicador mostra fórmula, n/N, fonte e "atualizado em". Indicador cuja definição ainda não foi aprovada aparece como "aguardando definição", nunca como percentual.

## Limites e dependências

- **Inclui:**
  - indicador (a) usando D-F1-01 (contrato ativo) e D-F1-07 (denominador);
  - indicador (b) agrupado por unidade, com filtro de período funcional;
  - indicador (c): para cada fonte, a proporção de contratos ativos com dado daquela fonte e a lista dos que não têm;
  - clique em cada indicador abre a lista de contratos que o compõe.
- **Fora de escopo:** rastreabilidade de 95% da produção e do estoque (Fase 3); disponibilidade futura (Fase 3); margem e resultado (fora até definição financeira).
- **Entradas e pré-condições:** Visão Geral provisória (SPEC-F2-02); decisões D-F1-01, D-F1-04 e D-F1-07 (SPEC-F2-04); cadastro de fontes (SPEC-F2-07).
- **Saídas/artefatos:** `Index.tsx` com os três indicadores; funções de cálculo em `services/`; nota de fórmula em cada cartão.
- **Dependências e responsáveis:** Champion; patrocinador confere os números com a amostra.
- **Risco e plano B:** sem D-F1-07 aprovada, o indicador (a) mostra n e N separados, com o rótulo "aguardando definição", sem percentual.
- **Rollback ou reversão:** voltar para a Visão Geral provisória.

## Fluxo e regras

1. Calcular N = contratos ativos (D-F1-01) no universo D-F1-07; n = os que têm quantidade, entregue e saldo calculado ou explicitamente marcados como indisponíveis, com fonte e data.
2. Somar contratado e entregue apenas dentro da mesma unidade; nunca converter sem a tabela aprovada (D-F1-04).
3. Fonte com situação diferente de `integrada` entra na cobertura como lacuna declarada.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Amostra carregada e decisões aprovadas | n/N, fórmula e lista clicável | — |
| Limite | Itens em m³ e em peças | Dois totais separados | — |
| Pendente | D-F1-07 não aprovada | "Aguardando definição" | Registrar a decisão |
| Lacuna | Omie ainda não integrado | Cobertura do Omie = lacuna | — |

## Checklist de execução

- [ ] Indicador (a) com fórmula e lista.
- [ ] Indicador (b) por unidade, com filtro de período.
- [ ] Indicador (c) por fonte, com lacunas.
- [ ] Conferência com consulta direta à base.

## Critérios de aceite

- [ ] **CA-F2-14-1:** o indicador (a) mostra n/N e a fórmula; o clique abre a lista que soma N.
- [ ] **CA-F2-14-2:** o indicador (b) agrupa por unidade, e nenhum total soma unidades diferentes.
- [ ] **CA-F2-14-3:** o indicador (c) mostra, para cada fonte, a proporção de contratos com dado dela e a lista dos que não têm.
- [ ] **CA-F2-14-4:** sem D-F1-07 aprovada, aparece "aguardando definição" no lugar do percentual.
- [ ] **CA-F2-14-5:** os valores da amostra conferem com consulta direta à base.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Visão Geral provisória | Abrir `/` | Sem n/N, sem cobertura | Captura |
| GREEN | CA-F2-14-1 a -3 | Abrir com a amostra; clicar nos cartões; mudar o período | Conforme os critérios | Capturas |
| REFACTOR/REGRESSÃO | CA-F2-14-4 e -5 | Desaprovar D-F1-07 em homologação; consultar a base | Rótulo pendente; números iguais | Capturas e consulta |

**Dados/fixtures:** amostra real (SPEC-F2-04) com itens em pelo menos duas unidades.
**Caminhos de erro obrigatórios:** universo vazio, unidade nula, fonte sem execução.
**Evidência exigida:** capturas e consulta de conferência.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T46 | Publicar o indicador de contratos ativos com saldo (n/N), com fórmula, lista e estado pendente | Fábio Pimentel (Champion) | SPEC-F2-14 | n/N com fórmula e lista clicável.<br>Sem D-F1-07, mostra 'aguardando definição'. | CA-F2-14-1, -4 e -5 (n/N) | A fazer |
| F2-T47 | Publicar contratado × entregue por unidade e período, sem misturar unidades | Fábio Pimentel (Champion) | SPEC-F2-14 | Totais separados por unidade.<br>Filtro de período funcional. | CA-F2-14-2 e -5 (totais) | A fazer |
| F2-T48 | Publicar a cobertura por fonte com lista de contratos sem dado e lacunas | Fábio Pimentel (Champion) | SPEC-F2-14 | Proporção por fonte.<br>Fonte não integrada como lacuna. | CA-F2-14-3 e -5 (cobertura) | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
