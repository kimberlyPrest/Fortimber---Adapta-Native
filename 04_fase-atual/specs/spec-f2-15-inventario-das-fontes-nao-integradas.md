# SPEC-F2-15 — Inventário técnico das fontes ainda não integradas

**Fase:** 2 — Onda 3 (reconciliação e visibilidade)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §7 (mapa inicial de fontes) e §12 item 5; §8 Fase 2 ("inventário de API e carga de Siscomex/porto/Dolphin/SISAP por prioridade acordada")
**Degrau da solução:** reuso — uma ficha por fonte em `03_documentos/fontes/`; sem código.

## Resultado observável

Siscomex, o sistema do porto, o SISAP e o Dolphin têm, cada um, uma ficha de viabilidade com recomendação. O patrocinador registra a decisão de integrar, adiar ou descartar cada fonte na Fase 3, com prioridade. Assim, a Fase 3 começa com as fontes de produção e expedição já qualificadas.

## Limites e dependências

- **Inclui:** ficha por fonte com:
  - o que oferece e as entidades úteis ao ciclo contrato → produção → expedição → entrega;
  - forma de acesso (API, banco de leitura, exportação de arquivo);
  - credencial necessária e quem a concede;
  - limites técnicos e termos de uso;
  - custo, dono e contato do fornecedor;
  - riscos e evidência (documentação lida, teste de acesso feito ou negado);
  - recomendação.
- **Fora de escopo:** construir conectores para essas fontes; Sysflora (passa para a Fase 3); scraping de portal.
- **Entradas e pré-condições:** contatos dos fornecedores; acesso aos ambientes que já existem.
- **Saídas/artefatos:** `siscomex.md`, `porto.md`, `sisap.md`, `dolphin.md` e `decisao-fontes-fase-3.md`.
- **Dependências e responsáveis:** Champion (Siscomex); TI (porto e SISAP); Produção (Dolphin); consultora consolida.
- **Risco e plano B:** fornecedor que não responde → ficha marcada "sem resposta", com a data do contato e a recomendação "adiar".
- **Rollback ou reversão:** não se aplica (documental).

## Fluxo e regras

1. Cada dono preenche a ficha da sua fonte.
2. Nenhuma ficha contém credencial, senha ou chave; só o nome do segredo e quem o guarda.
3. A consultora consolida e propõe a prioridade; o patrocinador decide.
4. A situação de cada fonte é atualizada na tela Fontes (SPEC-F2-07).

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Fonte com API documentada | Ficha completa, com recomendação "integrar" | — |
| Limite | Fornecedor sem resposta | Ficha "sem resposta", recomendação "adiar" | Novo contato com prazo |
| Falha | Acesso exige senha pessoal rotativa | Recomendação "não integrar" até haver mecanismo seguro | Rever na Fase 3 |

## Checklist de execução

- [ ] Ficha do Siscomex.
- [ ] Ficha do porto.
- [ ] Ficha do SISAP.
- [ ] Ficha do Dolphin.
- [ ] Decisão consolidada e registrada.
- [ ] Situação atualizada na tela Fontes.

## Critérios de aceite

- [ ] **CA-F2-15-1:** quatro fichas completas, com todos os campos preenchidos ou "sem resposta" com data.
- [ ] **CA-F2-15-2:** cada ficha tem recomendação e evidência (documentação ou teste de acesso).
- [ ] **CA-F2-15-3:** decisão integrar, adiar ou descartar registrada pelo patrocinador, com prioridade para a Fase 3.
- [ ] **CA-F2-15-4:** nenhuma ficha contém credencial ou valor de segredo.

## TDD da SPEC

Entrega não técnica: roteiro de verificação.

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Procurar fichas em `03_documentos/fontes/` | Inexistentes | — |
| GREEN | CA-F2-15-1 a -3 | Revisão das fichas pela consultora | Completas, com decisão | Documentos |
| REFACTOR/REGRESSÃO | CA-F2-15-4 | Procurar padrões de segredo nos documentos | Nada encontrado | Saída da busca |

**Dados/fixtures:** documentação pública e dos fornecedores.
**Caminhos de erro obrigatórios:** sem resposta, acesso negado, custo não aprovado.
**Evidência exigida:** fichas e decisão no repo do cliente.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T49 | Preencher a ficha de viabilidade do Siscomex | Fábio Pimentel (Champion) | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (Siscomex) | A fazer |
| F2-T50 | Preencher a ficha de viabilidade do sistema do porto | TI Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (porto) | A fazer |
| F2-T51 | Preencher a ficha de viabilidade do SISAP (cópia somente leitura com o fornecedor) | TI Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (SISAP) | A fazer |
| F2-T52 | Preencher a ficha de viabilidade do Dolphin | Produção Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (Dolphin) | A fazer |
| F2-T53 | Consolidar as fichas e registrar a decisão integrar, adiar ou descartar para a Fase 3 | Consultora Adapta (Kimberly Prestes) | SPEC-F2-15 | Decisão do patrocinador registrada com prioridade.<br>Situação atualizada na tela Fontes. | CA-F2-15-3 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
