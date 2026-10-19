# Fase 2 — Índice de SPECs

## Objetivo da fase

Fazer os dados de contratos e entregas entrarem de forma repetível, versionada e reconciliável a partir das fontes autorizadas, depois de estabilizar o que a Fase 1 entregou. Cada número exibido tem fonte, data de extração e estado de qualidade; diferenças entre fontes ficam visíveis e resolvidas por pessoas.

## Ordem recomendada

| Onda | SPEC | Entrega | Dependências principais |
|---|---|---|---|
| 0 | [SPEC-F2-01 — Saldo correto com várias fontes](spec-f2-01-saldo-correto-com-varias-fontes.md) | Saldo sem dupla contagem, recalculado em toda mudança de evento. | Nenhuma (preferência vem da F2-07). |
| 0 | [SPEC-F2-02 — Visão Geral sem dados fictícios](spec-f2-02-visao-geral-sem-dados-ficticios.md) | Página inicial e cabeçalho só com dados reais. | Nenhuma. |
| 0 | [SPEC-F2-03 — Acesso endurecido e UAT atribuível](spec-f2-03-acesso-endurecido-e-uat-atribuivel.md) | Criação de usuário só por admin, leitura por papel, login único, UAT com ator. | Usuários reais da Fortimber. |
| 0 | [SPEC-F2-04 — Regras de negócio aprovadas](spec-f2-04-regras-de-negocio-aprovadas.md) | D-F1-01 a D-F1-07 decididas; amostra real escolhida. | Patrocinador, Comercial, Produção. |
| 0 | [SPEC-F2-05 — Arquitetura, ambientes e operação](spec-f2-05-arquitetura-ambientes-e-operacao.md) | ADR-001, ambientes, backup testado, segredos inventariados. | TI Fortimber. |
| 0 | [SPEC-F2-06 — UAT da Fase 1 com amostra real](spec-f2-06-uat-da-fase-1-com-amostra-real.md) | Oito cenários da F1 com dados e pessoas reais; decisão de piloto. | F2-01, F2-03, F2-04. |
| 1 | [SPEC-F2-07 — Cadastro de fontes e fonte da verdade](spec-f2-07-cadastro-de-fontes-e-fonte-da-verdade.md) | Tela Fontes e preferência por campo com vigência. | F2-04 (D-F1-06). |
| 1 | [SPEC-F2-08 — Sincronização versionada](spec-f2-08-sincronizacao-versionada.md) | Motor com revisões, versões, execuções e rollback. | F2-01. |
| 1 | [SPEC-F2-09 — Conta de serviço e API de ingestão](spec-f2-09-conta-de-servico-e-api-de-ingestao.md) | Endpoint autenticado por fonte para os jobs. | F2-03, F2-07, F2-08. |
| 2 | [SPEC-F2-10 — Conector de contratos (área de dados)](spec-f2-10-conector-de-contratos-area-de-dados.md) | Contratos do Postgres entrando automaticamente. | F2-04, F2-09. |
| 2 | [SPEC-F2-11 — Conector Omie (faturamento)](spec-f2-11-conector-omie-faturamento.md) | Faturamentos ligados ao contrato, contagem pela D-F1-02. | F2-01, F2-09, F2-12. |
| 3 | [SPEC-F2-12 — Fila de divergências](spec-f2-12-fila-de-divergencias.md) | Diferenças entre fontes numa fila com resolução justificada. | F2-08. |
| 3 | [SPEC-F2-13 — Saúde das fontes e frescor](spec-f2-13-saude-das-fontes-e-frescor.md) | Estado e atraso por fonte; datas de extração e carga separadas. | F2-07, F2-08. |
| 3 | [SPEC-F2-14 — Indicadores reais e cobertura](spec-f2-14-indicadores-reais-e-cobertura.md) | n/N de contratos ativos, contratado × entregue, cobertura por fonte. | F2-02, F2-04, F2-07. |
| 3 | [SPEC-F2-15 — Inventário das fontes não integradas](spec-f2-15-inventario-das-fontes-nao-integradas.md) | Fichas de Siscomex, porto, SISAP e Dolphin; decisão para a F3. | Fornecedores e TI. |
| 3 | [SPEC-F2-16 — UAT e aceite da Fase 2](spec-f2-16-uat-e-aceite-da-fase-2.md) | Roteiro executado e aceite da fase. | Todas as anteriores. |

## Definições comuns

- **Fonte:** sistema ou arquivo cadastrado na tela Fontes (SPEC-F2-07), com situação e dono.
- **Execução:** uma rodada de sincronização de uma fonte, registrada em `execucoes_fonte` (SPEC-F2-08).
- **Extraído em × carregado em:** a hora em que o dado saiu da fonte e a hora em que entrou no sistema; as duas aparecem separadas.
- **Divergência:** valores diferentes para o mesmo fato vindos de fontes distintas, ou dado que não pôde ser vinculado; nunca é resolvida em silêncio.
- **Premissa não aprovada:** regra provisória em uso enquanto a decisão de negócio não é registrada; sempre rotulada na tela.

## Critério global de conclusão

A Fortimber executa o roteiro de UAT da Fase 2 (SPEC-F2-16) com dados reais das fontes integradas e registra o aceite ou as ressalvas com dono e prazo. Nenhum indicador é divulgado sem denominador aprovado, e nenhuma fonte não integrada aparece como se estivesse completa.
