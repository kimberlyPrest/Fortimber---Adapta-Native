# SPEC-F2-16 — UAT e aceite da Fase 2

**Fase:** 2 — Onda 3 (aceite)
**Status:** planejada
**Dono:** Comercial Fortimber
**Origem no escopo:** Escopo §8 Fase 2 (aceite binário); §14 (critério de encerramento); SPEC-F1-04 §7 (modelo de UAT)
**Degrau da solução:** reuso — tela e hooks de UAT da Fase 1, já atribuíveis (SPEC-F2-03), com o cadastro de novos cenários.

## Resultado observável

Representantes da Fortimber executam um roteiro de UAT da Fase 2, cobrindo cada fonte integrada e cada capacidade nova. Cada cenário registra esperado, observado, evidência e quem respondeu. O patrocinador registra o aceite da fase ou os bloqueios com dono e prazo.

## Limites e dependências

- **Inclui:** roteiro com pelo menos 12 cenários:
  1. reexecução sem duplicar;
  2. atualização de contrato com versão anterior;
  3. aditivo;
  4. mesma entrega em duas fontes;
  5. divergência e resolução;
  6. faturamento sem vínculo;
  7. falha de fonte visível;
  8. fonte atrasada;
  9. conta de serviço fora da sua fonte;
  10. rollback de execução;
  11. indicador n/N conferido;
  12. acesso por papel.

  Mais o cadastro desses cenários na tela de UAT, a execução e a decisão.
- **Fora de escopo:** cenários de produção, estoque e disponibilidade (Fase 3).
- **Entradas e pré-condições:** SPECs F2-01 a F2-15 concluídas ou com bloqueio registrado.
- **Saídas/artefatos:** roteiro em `03_documentos/uat/roteiro-fase-2.md`; respostas no sistema; ata exportada para `05_entregas/fase-2/`.
- **Dependências e responsáveis:** consultora redige o roteiro; Comercial e Produção executam; patrocinador decide.
- **Risco e plano B:** conector bloqueado por terceiro → o cenário vira "bloqueado com dono"; a fase pode ser aceita com ressalva, nunca com o cenário marcado como aprovado.
- **Rollback ou reversão:** a UAT pode ser repetida; a decisão nova substitui a anterior, mantendo o histórico.

## Fluxo e regras

1. A consultora redige o roteiro, cada cenário ligado aos CAs das SPECs.
2. O Champion cadastra os cenários na tela de UAT.
3. Os representantes executam com as próprias contas.
4. O patrocinador decide e exporta a ata.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Todos os cenários aprovados | Aceite da fase registrado | — |
| Limite | Cenário bloqueado por terceiro | Ressalva com dono e prazo | Reexecutar depois |
| Falha | Cenário reprovado | Classificado na SPEC de origem; correção e nova rodada | — |

## Checklist de execução

- [ ] Roteiro redigido e ligado aos CAs.
- [ ] Cenários cadastrados no sistema.
- [ ] Cenários executados com contas individuais.
- [ ] Decisão registrada e ata exportada.

## Critérios de aceite

- [ ] **CA-F2-16-1:** roteiro com pelo menos 12 cenários, cada um apontando para CAs das SPECs F2-01 a F2-14.
- [ ] **CA-F2-16-2:** cada cenário com esperado, observado, evidência e `respondido_por`.
- [ ] **CA-F2-16-3:** cada item do aceite binário da Fase 2 (escopo §8) marcado como atendido ou com ressalva.
- [ ] **CA-F2-16-4:** decisão de aceite registrada pelo patrocinador e ata em `05_entregas/fase-2/`.
- [ ] **CA-F2-16-5:** toda ressalva com dono e prazo.

## TDD da SPEC

Aceite humano: roteiro de verificação.

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Antes da execução | Listar os cenários da Fase 2 no sistema | Nenhum cadastrado | Captura |
| GREEN | CA-F2-16-1 a -4 | Executar o roteiro | Respostas e decisão registradas | Ata exportada |
| REFACTOR/REGRESSÃO | CA-F2-16-5 e regressão da Fase 1 | Reexecutar os oito cenários da Fase 1 | Sem regressão; ressalvas com dono | Ata |

**Dados/fixtures:** amostra real e execuções reais das fontes integradas.
**Caminhos de erro obrigatórios:** fonte fora do ar no dia da UAT, usuário sem papel, divergência inesperada.
**Evidência exigida:** ata da UAT e roteiro preenchido.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T54 | Redigir o roteiro de UAT da Fase 2, com 12 ou mais cenários ligados aos CAs | Consultora Adapta (Kimberly Prestes) | SPEC-F2-16 | Cada cenário aponta para CAs das SPECs F2-01 a F2-14. | CA-F2-16-1 | A fazer |
| F2-T55 | Executar o roteiro de UAT da Fase 2 com contas individuais | Comercial Fortimber | SPEC-F2-16 | Cada cenário com esperado, observado, evidência e `respondido_por`.<br>Aceite binário do escopo §8 marcado item a item. | CA-F2-16-2 e CA-F2-16-3 | A fazer |
| F2-T56 | Registrar a decisão de aceite da Fase 2 e as ressalvas com dono e prazo | Patrocinador Fortimber | SPEC-F2-16 | Decisão registrada e ata em `05_entregas/fase-2/`.<br>Ressalvas com dono e prazo. | CA-F2-16-4 e CA-F2-16-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
