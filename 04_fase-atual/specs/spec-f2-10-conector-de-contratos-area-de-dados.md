# SPEC-F2-10 — Conector de contratos (área de dados Postgres → sistema)

**Fase:** 2 — Onda 2 (conectores)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §7 (área de dados/Postgres em VM como camada candidata); §8 Fase 2 ("conector de leitura para a primeira fonte priorizada")
**Degrau da solução:** reuso — aproveita o pipeline Python na VM e a extração de ~4.500 linhas de contratos que o Champion já mantém; sem serviço novo.

## Resultado observável

Os contratos e itens da área de dados entram no sistema automaticamente, na frequência acordada, sem duplicar, cada execução visível na tela de saúde das fontes. Uma amostra de 30 contratos confere, campo a campo, com o Postgres.

## Limites e dependências

- **Inclui:**
  - usuário de banco somente leitura para o conector;
  - views `vw_contratos_canonico` e `vw_itens_canonico` no esquema canônico da Fase 1;
  - documento de mapeamento campo a campo (origem → canônico, transformação, unidade);
  - job Python agendado na VM (cron ou timer do systemd) que lê as views, monta lotes de até 5.000 registros, envia à API de ingestão com a conta de serviço e grava um log local;
  - nova tentativa em falha (até 3 vezes, com espera crescente);
  - frequência proposta: diária às 06h, a confirmar pelo Comercial e registrada em `fontes.frequencia_acordada_min`.
- **Fora de escopo:** eventos de entrega (vêm do Omie e da planilha); escrita nas tabelas de origem; o SISAP como fonte (só inventário, SPEC-F2-15).
- **Entradas e pré-condições:** API de ingestão (SPEC-F2-09); motor (SPEC-F2-08); decisões D-F1-01 e D-F1-04 para o mapeamento de estado e unidade.
- **Saídas/artefatos:** views; job versionado no repositório da área de dados; `03_documentos/integracao/mapeamento-area-de-dados.md`; execuções em `execucoes_fonte`.
- **Dependências e responsáveis:** TI cria o usuário de banco; Champion faz o mapeamento e o job; Comercial reconcilia a amostra.
- **Risco e plano B:** se a extração tiver chaves instáveis (por exemplo, número de contrato reutilizado), usar a chave composta definida no mapeamento e mandar as colisões para a fila de divergências; nunca mesclar.
- **Rollback ou reversão:** reverter a execução pelo motor; suspender o agendamento.

## Fluxo e regras

1. O job lê as views com o usuário somente leitura.
2. Converte para o payload `contrato-ingestao-v1`, com `sistema_origem = area-de-dados-postgres` e `extraido_em` igual ao horário da leitura.
3. Envia os lotes; registra a resposta.
4. Em erro 5xx ou timeout, tenta de novo; em 4xx, não tenta de novo e registra o erro.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Primeira execução | Universo selecionado carregado | — |
| Repetição | Segunda execução sem mudança na origem | 0 alterações | — |
| Mudança | Quantidade alterada no Postgres | Atualizada no sistema, com versão anterior | Rollback pela execução |
| Falha | API fora do ar | Até 3 tentativas; falha registrada e visível | Execução manual depois |

## Checklist de execução

- [ ] Usuário de banco somente leitura criado e testado.
- [ ] Views canônicas criadas.
- [ ] Mapeamento documentado.
- [ ] Job agendado com novas tentativas e log.
- [ ] Frequência acordada registrada.
- [ ] Amostra reconciliada.

## Critérios de aceite

- [ ] **CA-F2-10-1:** o usuário de banco do conector recebe erro de permissão ao tentar INSERT, UPDATE ou DELETE.
- [ ] **CA-F2-10-2:** a primeira execução carrega o universo selecionado; a segunda, sem mudança na origem, registra 0 alterações.
- [ ] **CA-F2-10-3:** uma quantidade alterada no Postgres aparece no sistema na execução seguinte, com a versão anterior preservada.
- [ ] **CA-F2-10-4:** com a API indisponível, o job tenta de novo até o limite e a falha aparece na saúde das fontes.
- [ ] **CA-F2-10-5:** amostra de 30 contratos com campos-chave iguais ao Postgres, em planilha esperado × observado assinada pelo Comercial.
- [ ] **CA-F2-10-6:** mapeamento campo a campo publicado no repo do cliente.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Procurar contratos com `sistema_origem = area-de-dados-postgres` | Nenhum | Consulta |
| GREEN | CA-F2-10-1 a -4 | Teste de permissão; duas execuções; alteração controlada em homologação; API desligada | Conforme os critérios | Logs do job e execuções |
| REFACTOR/REGRESSÃO | CA-F2-10-5 e -6 | Reconciliar a amostra; revisar o mapeamento | Planilha assinada | Planilha e documento |

**Dados/fixtures:** cópia de homologação da área de dados ou recorte da amostra aprovada.
**Caminhos de erro obrigatórios:** view vazia, chave nula, unidade desconhecida, timeout, lote parcial.
**Evidência exigida:** logs do job (sem credenciais), relatórios de execução e planilha de reconciliação.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T32 | Criar o usuário de banco somente leitura para o conector na área de dados | TI Fortimber | SPEC-F2-10 | INSERT, UPDATE e DELETE recusados.<br>Acesso só às views do conector. | CA-F2-10-1 | A fazer |
| F2-T33 | Criar as views canônicas de contratos e itens e publicar o mapeamento campo a campo | Fábio Pimentel (Champion) | SPEC-F2-10 | Views no esquema canônico.<br>Mapeamento com transformação e unidade. | CA-F2-10-6 | A fazer |
| F2-T34 | Construir e agendar o job Python que envia contratos à ingestão, com novas tentativas e log | Fábio Pimentel (Champion) | SPEC-F2-10 | Primeira execução carrega o universo; segunda, 0 alterações.<br>Alteração aparece com versão.<br>Falha tenta de novo e fica visível. | CA-F2-10-2, -3 e -4 | A fazer |
| F2-T35 | Reconciliar 30 contratos da amostra entre o sistema e o Postgres | Comercial Fortimber | SPEC-F2-10 | Planilha esperado × observado assinada.<br>Diferenças classificadas. | CA-F2-10-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
