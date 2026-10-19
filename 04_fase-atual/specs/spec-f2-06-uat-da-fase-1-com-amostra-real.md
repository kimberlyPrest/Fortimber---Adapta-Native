# SPEC-F2-06 — UAT da Fase 1 com amostra real

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** Comercial Fortimber
**Origem no escopo:** Escopo §8 Fase 1 (aceite binário a–e); SPEC-F1-04 §7–8
**Degrau da solução:** reuso — tela e hooks de UAT e carga CSV existentes, depois das correções das SPECs F2-01 e F2-03.

## Resultado observável

Os oito cenários de UAT da Fase 1 são executados por representantes reais, com contas individuais, sobre a amostra real aprovada. A decisão de piloto fica registrada no sistema e exportada para `05_entregas/`. Com isso, as ressalvas de aceite da Fase 1 ficam fechadas ou registradas como bloqueios conhecidos.

## Limites e dependências

- **Inclui:** carga da amostra pela tela de Cargas atual; os cenários `nacional-simples`, `exportacao-parcial`, `aditivo`, `campo-ausente`, `duplicidade`, `carga-invalida`, `acesso-negado` e `fonte-desatualizada`; planilha de esperado × observado; decisão de piloto.
- **Fora de escopo:** conectores automáticos (Onda 2); cenários novos da Fase 2 (SPEC-F2-16).
- **Entradas e pré-condições:** amostra aprovada (SPEC-F2-04); saldo multifonte corrigido (SPEC-F2-01); contas individuais e UAT atribuível (SPEC-F2-03).
- **Saídas/artefatos:** carga publicada; respostas da UAT com `respondido_por`; registro em `uat_decisoes`; ata exportada em `05_entregas/fase-1/uat-amostra-real.md`.
- **Dependências e responsáveis:** Champion carrega a amostra; Comercial e Produção executam; patrocinador decide.
- **Risco e plano B:** o cenário `aditivo` depende da SPEC-F2-08. Se ela não estiver pronta, o cenário é marcado "bloqueado — SPEC-F2-08", nunca "aprovado".
- **Rollback ou reversão:** reverter a carga pela tela de Cargas; a UAT pode ser repetida.

## Fluxo e regras

1. O Champion gera o CSV da amostra a partir da área de dados, no template v1 da Fase 1, sem dados pessoais além do necessário.
2. O Champion pré-valida e publica; o relatório de carga não pode ter rejeição sem explicação.
3. Cada representante faz login com a própria conta e responde os cenários do seu papel.
4. A planilha de referência (valores esperados assinados pelo Comercial) é comparada com o que a tela mostra.
5. O aprovador registra a decisão de piloto: aprovado, bloqueios registrados ou rejeitado.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Amostra publicada e cenários respondidos | Decisão registrada com respondentes reais | — |
| Limite | Cenário `aditivo` sem suporte | Marcado como bloqueado, com a SPEC indicada | Reexecutar após a SPEC-F2-08 |
| Falha | Saldo diverge da planilha | Cenário reprovado, classificado na SPEC certa | Corrigir e repetir |

## Checklist de execução

- [ ] CSV da amostra gerado e pré-validado.
- [ ] Carga publicada com relatório explicado.
- [ ] Oito cenários respondidos com contas individuais.
- [ ] Planilha esperado × observado assinada.
- [ ] Decisão de piloto registrada e exportada.

## Critérios de aceite

- [ ] **CA-F2-06-1:** amostra publicada pela carga CSV, com todas as rejeições do relatório explicadas.
- [ ] **CA-F2-06-2:** cada um dos oito cenários tem pelo menos uma resposta de representante funcional, com `respondido_por` preenchido.
- [ ] **CA-F2-06-3:** os saldos da amostra conferem com a planilha de referência, ou cada diferença está classificada (SPEC-F1-01, F1-02 ou F1-03).
- [ ] **CA-F2-06-4:** decisão de piloto registrada pelo aprovador em `uat_decisoes` e ata exportada para `05_entregas/fase-1/`.
- [ ] **CA-F2-06-5:** nenhum cenário dependente de funcionalidade inexistente aparece como aprovado.

## TDD da SPEC

Aceite humano: roteiro de verificação sobre o sistema.

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Consultar `uat_execucoes` | Respostas sem ator, sobre fixtures fictícias | Exportação atual |
| GREEN | CA-F2-06-1 a -4 | Executar o roteiro | Respostas atribuídas e decisão registrada | Ata exportada |
| REFACTOR/REGRESSÃO | CA-F2-06-5 | Revisar cada cenário aprovado contra o código | Nenhum aprovado indevido | Nota da consultora |

**Dados/fixtures:** amostra real de 20 a 30 contratos (SPEC-F2-04).
**Caminhos de erro obrigatórios:** carga com rejeição; usuário sem papel UAT; divergência de saldo.
**Evidência exigida:** ata da UAT, planilha esperado × observado e relatório de carga.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T18 | Gerar o CSV da amostra real a partir da área de dados e publicá-lo pela carga atual | Fábio Pimentel (Champion) | SPEC-F2-06 | Pré-validação e publicação concluídas.<br>Toda rejeição explicada. | CA-F2-06-1 | A fazer |
| F2-T19 | Executar os oito cenários de UAT da Fase 1 com contas individuais e planilha esperado × observado | Comercial Fortimber | SPEC-F2-06 | Oito cenários respondidos com `respondido_por`.<br>Planilha assinada.<br>Cenários sem suporte marcados como bloqueados. | CA-F2-06-2, -3 e -5 | A fazer |
| F2-T20 | Registrar a decisão de piloto da Fase 1 e exportar a ata para 05_entregas | Patrocinador Fortimber | SPEC-F2-06 | Decisão em `uat_decisoes`.<br>Ata em `05_entregas/fase-1/`. | CA-F2-06-4 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
