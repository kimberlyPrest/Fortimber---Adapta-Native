# SPEC-F2-07 — Cadastro de fontes e fonte da verdade por campo

**Fase:** 2 — Onda 1 (fundação de integração)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §7 (mapa inicial de fontes), §8 Fase 2 ("definição de fonte da verdade por campo", "cobertura explícita por fonte"); D-F1-06
**Degrau da solução:** construção mínima — duas coleções no PocketBase e uma tela no padrão das telas existentes; nenhuma dependência nova.

## Resultado observável

A tela "Fontes" lista cada sistema com situação, dono, frequência acordada e último sucesso. Um administrador registra, por entidade e campo, qual fonte é a preferencial, com aprovador, data e tolerância. O saldo (SPEC-F2-01) usa a preferência vigente, e trocar uma preferência cria nova vigência sem apagar a anterior.

## Limites e dependências

- **Inclui:**
  - Coleção `fontes`: `codigo` (único), `nome`, `tipo` (banco, api, arquivo, manual), `dono`, `situacao` (integrada, em_integracao, planejada, bloqueada, fora_da_fase), `frequencia_acordada_min`, `observacao`. O campo `ultimo_sucesso` é derivado de `execucoes_fonte` (SPEC-F2-08).
  - Coleção `preferencias_campo`: `entidade`, `campo`, `fonte_preferencial` (relação), `tolerancia` (opcional), `aprovado_por`, `aprovado_em`, `vigente_desde`, `vigente_ate`.
  - Tela de leitura para todos os autenticados; edição só para admin.
  - Carga inicial das fontes: `area-de-dados-postgres`, `omie`, `excel-controle`, `carga-manual`, `siscomex`, `porto`, `sisap`, `dolphin` e `sysflora` (esta como `fora_da_fase`).
- **Fora de escopo:** cálculo de cobertura (SPEC-F2-14); saúde e atraso (SPEC-F2-13).
- **Entradas e pré-condições:** matriz D-F1-06 (SPEC-F2-04) para o preenchimento; papéis da SPEC-F2-03.
- **Saídas/artefatos:** migração das coleções; página `/fontes`; preferências cadastradas.
- **Dependências e responsáveis:** Champion constrói; Comercial preenche a matriz pela tela (como admin delegado) ou entrega ao Champion para cadastro.
- **Risco e plano B:** sem matriz aprovada, nenhuma preferência é cadastrada e a regra provisória da SPEC-F2-01 continua valendo.
- **Rollback ou reversão:** encerrar a vigência da preferência (`vigente_ate`); as coleções podem ficar vazias sem afetar o saldo.

## Fluxo e regras

1. Uma preferência só é salva com `aprovado_por` e `aprovado_em`.
2. Para cada (entidade, campo), só pode haver uma preferência vigente; a nova fecha a anterior com `vigente_ate`.
3. O saldo consulta a preferência vigente para (`contratos_eventos`, `quantidade_entregue`).
4. Fonte com `situacao` diferente de `integrada` aparece como lacuna nas telas que dependem dela.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Admin cadastra Omie como preferencial para quantidade entregue | Preferência vigente; saldo passa a usá-la | — |
| Limite | Nova preferência para o mesmo campo | A anterior ganha `vigente_ate`; a nova vale | Histórico consultável |
| Falha | Salvar sem aprovador | Recusa com mensagem | Preencher o aprovador |
| Falha | Leitor tenta editar | 403 | — |

## Checklist de execução

- [ ] Coleções criadas com regras por papel.
- [ ] Nove fontes cadastradas com situação.
- [ ] Tela `/fontes` com lista e histórico de preferências.
- [ ] Validação de aprovador e data.
- [ ] Matriz D-F1-06 cadastrada.

## Critérios de aceite

- [ ] **CA-F2-07-1:** a tela Fontes lista as nove fontes com situação, dono e frequência acordada.
- [ ] **CA-F2-07-2:** leitor não edita fontes nem preferências (403); admin edita.
- [ ] **CA-F2-07-3:** salvar uma preferência sem aprovador ou sem data é recusado.
- [ ] **CA-F2-07-4:** trocar a preferência de um campo cria nova vigência e mantém a anterior consultável.
- [ ] **CA-F2-07-5:** a matriz D-F1-06 aprovada está cadastrada e vigente.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Procurar a rota `/fontes` | Inexistente | — |
| GREEN | CA-F2-07-1 a -4 | Cadastrar, editar como admin e tentar editar como leitor | Conforme os critérios | Capturas |
| REFACTOR/REGRESSÃO | CA-F2-07-5 e CA-F2-01-4 | Cadastrar a matriz e conferir o saldo da fixture multifonte | Saldo segue a preferência | Captura do detalhe |

**Dados/fixtures:** fixture `FX-MULTI-01` (SPEC-F2-01).
**Caminhos de erro obrigatórios:** preferência sem aprovador; duas vigências simultâneas; fonte inexistente.
**Evidência exigida:** capturas da tela e do detalhe do contrato.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T21 | Criar a coleção `fontes`, cadastrar as nove fontes e publicar a tela Fontes (leitura) | Fábio Pimentel (Champion) | SPEC-F2-07 | Nove fontes com situação, dono e frequência.<br>Leitor só lê. | CA-F2-07-1 e CA-F2-07-2 (parte de fontes) | A fazer |
| F2-T22 | Criar `preferencias_campo` com vigência, aprovador obrigatório e edição só por admin | Fábio Pimentel (Champion) | SPEC-F2-07 | Recusa sem aprovador ou data.<br>Nova vigência fecha a anterior. | CA-F2-07-2 (parte de preferências), CA-F2-07-3 e -4 | A fazer |
| F2-T23 | Cadastrar no sistema a matriz D-F1-06 aprovada | Comercial Fortimber | SPEC-F2-07 | Uma preferência vigente por campo da matriz. | CA-F2-07-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
