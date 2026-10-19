# SPEC-F2-09 — Conta de serviço e API de ingestão

**Fase:** 2 — Onda 1 (fundação de integração)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §7 (privilégio mínimo, segredos por ambiente) e §9; §8 Fase 2 ("credenciais por mecanismo seguro e privilégio de leitura")
**Degrau da solução:** nativo da plataforma — coleção de autenticação separada e rota customizada do PocketBase, chamando o motor da SPEC-F2-08.

## Resultado observável

Os jobs de integração enviam lotes ao sistema por um endpoint autenticado, cada um com uma conta de serviço própria por fonte. Essas contas não entram na interface, não leem dados de usuários e só gravam na própria fonte. Cada lote aceito vira uma execução visível do motor de sincronização.

## Limites e dependências

- **Inclui:**
  1. Coleção de autenticação `contas_servico`, separada de `users`: `nome`, `fonte` (relação com `fontes`), `ativa`, `criada_por`. Sem acesso à interface.
  2. Endpoint `POST /backend/v1/ingestao/{fonte}`.
     - Corpo JSON: `execucao_externa_id`, `extraido_em`, `contratos[]`, `itens[]`, `eventos[]`, no esquema canônico da Fase 1.
     - Valida o esquema; limite de 5.000 registros e 5 MB por lote.
  3. A conta só envia para a fonte a que pertence; as listagens padrão de outras coleções ficam negadas para ela.
  4. Resposta com o relatório da execução (contagens e erros por linha, sem ecoar o conteúdo).
  5. Logs registram fonte, conta, contagens e duração; nunca o corpo nem a credencial.
  6. Revogação: desativar a conta invalida o próximo envio. O procedimento de rotação e revogação fica documentado no inventário de segredos (SPEC-F2-05).
- **Fora de escopo:** jobs dos conectores (SPECs F2-10 e F2-11); leitura de dados pela conta de serviço; webhooks de entrada.
- **Entradas e pré-condições:** motor versionado (SPEC-F2-08); regras de papel (SPEC-F2-03); cadastro de fontes (SPEC-F2-07).
- **Saídas/artefatos:** coleção, rota, contrato do payload em `03_documentos/integracao/contrato-ingestao-v1.md`, contas de serviço de `area-de-dados-postgres` e `omie`.
- **Dependências e responsáveis:** Champion; TI guarda e rotaciona as credenciais.
- **Risco e plano B:** se a coleção de autenticação separada não for viável no plano do Skip, usar `users` com `role = integracao` e regras explícitas de negação, registrando o risco na ADR-001.
- **Rollback ou reversão:** desativar as contas; a rota pode ser desligada sem afetar a carga manual.

## Fluxo e regras

1. O job autentica com a conta de serviço da fonte.
2. Envia o lote para `/backend/v1/ingestao/{fonte}`.
3. O sistema valida conta, fonte, tamanho e esquema; se algo falhar, responde 4xx sem alterar dados.
4. O sistema chama o motor e devolve o relatório da execução.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Conta do Postgres envia lote válido | Execução criada com contagens | — |
| Limite | Lote com 5.001 registros | 400 sem alteração | Dividir o lote |
| Falha | Conta do Postgres envia para `omie` | 403 | — |
| Falha | Conta desativada | 401 | Reativar ou rotacionar |

## Checklist de execução

- [ ] Coleção `contas_servico` com regras.
- [ ] Endpoint de ingestão com validação de esquema e limites.
- [ ] Negação de leitura das demais coleções.
- [ ] Logs sem conteúdo nem credenciais.
- [ ] Contrato do payload documentado.
- [ ] Rotação e revogação documentadas e testadas.

## Critérios de aceite

- [ ] **CA-F2-09-1:** a conta de serviço do Postgres envia um lote válido e a execução aparece com as contagens corretas.
- [ ] **CA-F2-09-2:** a mesma conta enviando para `omie` recebe 403.
- [ ] **CA-F2-09-3:** a conta de serviço listando `users` ou `contratos` pela API padrão recebe 403 ou lista vazia, e não acessa a interface.
- [ ] **CA-F2-09-4:** lote acima do limite ou com JSON inválido recebe 400, sem alteração de dados.
- [ ] **CA-F2-09-5:** conta desativada recebe 401 no envio seguinte.
- [ ] **CA-F2-09-6:** nenhuma credencial ou conteúdo de lote aparece em log, repositório ou resposta.

## TDD da SPEC

Prova por cenário demonstrado com chamadas HTTP; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Chamar `/backend/v1/ingestao/area-de-dados-postgres` | 404 | Resposta |
| GREEN | CA-F2-09-1 a -5 | Enviar lotes válidos, inválidos, para a fonte errada e com conta revogada | Conforme os critérios | Respostas, sem credenciais |
| REFACTOR/REGRESSÃO | CA-F2-09-6 | Inspecionar os logs do PocketBase e procurar padrões de segredo no repositório | Nada encontrado | Saída da busca |

**Dados/fixtures:** lotes `L1`–`L5` da SPEC-F2-08 em JSON.
**Caminhos de erro obrigatórios:** esquema inválido, fonte inexistente, conta inativa, timeout do motor.
**Evidência exigida:** respostas HTTP e trecho de log sem dados sensíveis.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T29 | Criar a coleção `contas_servico`, uma conta por fonte, sem acesso à interface nem a outras coleções | Fábio Pimentel (Champion) | SPEC-F2-09 | Contas das fontes Postgres e Omie criadas.<br>Listagem de `users` e `contratos` negada. | CA-F2-09-3 | A fazer |
| F2-T30 | Criar o endpoint de ingestão com validação de esquema, limites, restrição por fonte e logs sem conteúdo | Fábio Pimentel (Champion) | SPEC-F2-09 | Lote válido vira execução.<br>Fonte errada recebe 403; lote inválido recebe 400.<br>Contrato do payload documentado. | CA-F2-09-1, -2, -4 e -6 | A fazer |
| F2-T31 | Documentar e testar a rotação e a revogação das contas de serviço | TI Fortimber | SPEC-F2-09 | Procedimento no inventário de segredos.<br>Conta revogada recebe 401. | CA-F2-09-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
