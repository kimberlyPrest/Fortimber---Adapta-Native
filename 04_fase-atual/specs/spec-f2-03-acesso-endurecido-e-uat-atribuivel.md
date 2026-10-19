# SPEC-F2-03 — Acesso endurecido e UAT atribuível

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §9 (privilégio mínimo, acesso individual); SPEC-F1-04 §3–4
**Degrau da solução:** nativo da plataforma — regras de API, coleções de autenticação e rotas do PocketBase; nenhuma biblioteca nova.

## Resultado observável

Só administradores criam usuários e alteram papéis. Cada papel só lê o que a matriz permite, no servidor e na interface. A credencial de sessão nunca aparece em URL. Existe uma única tela de login, e cada resposta de UAT fica ligada ao usuário que a registrou.

## Limites e dependências

- **Inclui:**
  1. `users.createRule` fechado: criação apenas por admin ou superuser.
  2. A promoção automática da migração `0007_f1t07_uat_admin.js` é neutralizada por uma nova migração; a conta compartilhada `uat@fortimber.local` é rebaixada ou removida assim que existirem contas individuais, e o e-mail deixa de aparecer no código.
  3. Leitura de `proveniencia_carregamentos` e `cargas` restrita a `operador` e `admin`. Para o `leitor`, o acesso fica negado até a Fortimber aprovar a linha "Ver proveniência operacional detalhada" da matriz (SPEC-F1-04 §3).
  4. `uat_execucoes.updateRule` fechado: escrita apenas pelo hook.
  5. Exportação da ata da UAT por requisição com cabeçalho de autorização e download do arquivo, sem credencial na URL.
  6. Campo `papel_uat` em `users` (champion, comercial, producao, aprovador), definido por admin; campo `respondido_por` (relação com `users`) em `uat_execucoes`; o hook recusa resposta para papel diferente do papel do usuário.
  7. Página `/login` única, com guarda de rota no `AppLayout`; os blocos de login internos de Contratos, Cargas e UAT são removidos, sem e-mail pré-preenchido.
- **Fora de escopo:** SSO e MFA (a TI decide em SPEC-F2-05; a implementação fica para fase posterior, salvo decisão); segregação por filial; contas de serviço (SPEC-F2-09).
- **Entradas e pré-condições:** matriz de capacidades da SPEC-F1-04 §3; lista de usuários reais fornecida pela Fortimber.
- **Saídas/artefatos:** migrações novas, hooks `uat_responder.js` e `uat_ata.js` revisados, página de login, `AppLayout` com guarda de rota.
- **Dependências e responsáveis:** usuários e papéis reais confirmados pela Fortimber; cadastro feito pelo Champion como admin.
- **Risco e plano B:** se faltar algum usuário real na data do UAT, a UAT daquele papel fica pendente; não se volta para conta compartilhada.
- **Rollback ou reversão:** as migrações têm `down`; restaurar regras anteriores só em ambiente de desenvolvimento.

## Fluxo e regras

1. Usuário não autenticado é redirecionado para `/login` em qualquer rota.
2. Depois do login, menu e telas mostram só o que o papel permite; o servidor aplica a mesma regra.
3. Admin cria usuário, define `role` e `papel_uat` e registra a ação no log com o ator.
4. Ao responder a UAT, o sistema usa o usuário logado como `respondido_por` e o seu `papel_uat`; o representante é o nome do usuário.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Leitor autenticado consulta contratos | Lista e detalhe disponíveis; proveniência operacional oculta | — |
| Limite | Operador tenta criar usuário pela API | 403 | Admin cria |
| Falha | Usuário comercial responde cenário como produção | 403 com mensagem neutra | Responder com o próprio papel |
| Falha | Ata pedida sem cabeçalho de autorização | 401 | Baixar pela tela autenticada |

## Checklist de execução

- [ ] Regras de criação e promoção de usuário fechadas.
- [ ] Regras de leitura por papel aplicadas no servidor.
- [ ] Interface oculta o que o papel não acessa.
- [ ] Exportação da ata sem credencial na URL.
- [ ] Campos `papel_uat` e `respondido_por` criados e usados.
- [ ] Login único e guarda de rota.
- [ ] Conta compartilhada desativada; e-mail removido do código.

## Critérios de aceite

- [ ] **CA-F2-03-1:** leitor ou operador autenticado tenta criar usuário pela API → 403; admin cria → sucesso.
- [ ] **CA-F2-03-2:** leitor chama a listagem de `proveniencia_carregamentos` e `cargas` pela API direta → 403 ou lista vazia; operador → dados.
- [ ] **CA-F2-03-3:** a URL aberta na exportação da ata não contém credencial; o endpoint chamado sem cabeçalho de autorização responde 401.
- [ ] **CA-F2-03-4:** usuário com `papel_uat = comercial` que responde um cenário como `producao` recebe recusa; a resposta com o próprio papel grava `respondido_por` com o id dele.
- [ ] **CA-F2-03-5:** acessar `/contratos` sem sessão redireciona para `/login`; o código não contém `uat@fortimber.local` nem e-mail pré-preenchido.
- [ ] **CA-F2-03-6:** operador continua pré-validando, publicando e revertendo cargas; leitor continua consultando lista e detalhe.

## TDD da SPEC

Prova por cenário demonstrado (chamadas diretas à API e uso da tela); registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Escalonamento atual | Com um leitor, criar pela API um usuário com `role = operador` | Sucesso indevido (falha reproduzida) | Resposta da API, sem credenciais |
| GREEN | CA-F2-03-1 a -5 | Repetir com cada papel; exportar a ata; acessar rotas sem sessão | Recusas e sucessos conforme a matriz | Tabela papel × ação × resultado |
| REFACTOR/REGRESSÃO | CA-F2-03-6 | Fluxo completo de carga com operador; consulta com leitor | Sem regressão | Capturas |

**Dados/fixtures:** quatro usuários de teste em homologação (leitor, operador, admin, aprovador) com `papel_uat` diferentes.
**Caminhos de erro obrigatórios:** sessão expirada, papel ausente, usuário desativado, rota direta por URL.
**Evidência exigida:** tabela papel × ação × resultado esperado/observado; capturas sem nenhuma credencial visível.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T06 | Fechar criação e promoção de usuários para admin e neutralizar a promoção automática da conta compartilhada | Fábio Pimentel (Champion) | SPEC-F2-03 | `users.createRule` só admin/superuser.<br>Migração nova neutraliza a 0007.<br>Conta compartilhada rebaixada ou removida. | CA-F2-03-1 e a parte de código do CA-F2-03-5 | A fazer |
| F2-T07 | Aplicar a leitura por papel em proveniência, cargas e UAT no servidor e ocultar na interface | Fábio Pimentel (Champion) | SPEC-F2-03 | Leitor sem acesso a `proveniencia_carregamentos` e `cargas`.<br>`uat_execucoes` sem escrita direta.<br>Menu e telas ocultam o que o papel não acessa. | CA-F2-03-2 e CA-F2-03-6 | A fazer |
| F2-T08 | Exportar a ata da UAT por requisição autenticada, sem credencial na URL | Fábio Pimentel (Champion) | SPEC-F2-03 | Download por requisição com cabeçalho de autorização.<br>Endpoint recusa chamada sem cabeçalho. | CA-F2-03-3 | A fazer |
| F2-T09 | Ligar cada resposta de UAT ao usuário logado e ao seu papel de UAT | Fábio Pimentel (Champion) | SPEC-F2-03 | Campos `papel_uat` e `respondido_por` criados.<br>Hook recusa papel diferente do usuário. | CA-F2-03-4 | A fazer |
| F2-T10 | Criar a tela de login única com guarda de rota e remover os logins internos das páginas | Fábio Pimentel (Champion) | SPEC-F2-03 | Rotas protegidas redirecionam para `/login`.<br>Nenhum e-mail pré-preenchido no código. | CA-F2-03-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
