# SPEC-F2-12 — Fila de divergências e reconciliação

**Fase:** 2 — Onda 3 (reconciliação e visibilidade)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §6 regra 5 ("preservar os valores originais e expor discrepância"); §8 Fase 2 ("fila/lista de divergências para revisão humana")
**Degrau da solução:** construção mínima — uma coleção e uma tela no padrão existente; a detecção roda dentro do motor da SPEC-F2-08.

## Resultado observável

Toda diferença detectada entre fontes ou dentro de um lote vira um item numa fila, com tipo, valor de cada fonte, contrato, estado e responsável. Um usuário autorizado resolve com justificativa, sem alterar a fonte. O item reabre sozinho se a fonte mudar de novo.

## Limites e dependências

- **Inclui:**
  - Coleção `divergencias`.
    - Tipos: `quantidade_divergente`, `presente_em_uma_fonte`, `unidade_incompativel`, `colisao_chave`, `evento_sem_vinculo` e `revisao_retroativa`.
    - Campos: contrato e item (quando houver), `valores` (json por fonte), `execucao`, `estado` (aberta, em_analise, resolvida, reaberta), `responsavel`, `resolucao` (aceitar_fonte, corrigir_na_origem, nao_e_divergencia), `fonte_aceita`, `justificativa`, `resolvido_por`, `resolvido_em`.
  - Detecção automática no fim de cada execução do motor.
  - Tela com filtros por tipo, estado, contrato, fonte e responsável.
  - Resolução "aceitar fonte X": grava uma preferência pontual para aquele item, usada pelo saldo (SPEC-F2-01) e registrada no histórico.
  - Reabertura quando uma execução posterior traz valor diferente do resolvido.
- **Fora de escopo:** corrigir dado no sistema de origem; resolução automática; notificação externa.
- **Entradas e pré-condições:** motor (SPEC-F2-08); saldo multifonte (SPEC-F2-01); papéis (SPEC-F2-03).
- **Saídas/artefatos:** coleção; tela `/divergencias`; detecção no motor; contador na Visão Geral.
- **Dependências e responsáveis:** Champion constrói; operadores e Comercial resolvem.
- **Risco e plano B:** se a primeira carga real gerar milhares de divergências, agrupar por tipo e contrato e priorizar contratos ativos com prazo mais próximo; não suprimir itens.
- **Rollback ou reversão:** reverter a execução remove as divergências que ela criou; resoluções ficam no histórico.

## Fluxo e regras

1. O motor compara os valores por fonte para os campos com preferência ou com mais de uma fonte.
2. Diferença acima da tolerância (ou qualquer diferença, sem tolerância aprovada) → divergência `aberta`.
3. Operador ou admin assume (`em_analise`), decide e justifica.
4. A resolução registra ator e data; `aceitar_fonte` altera o saldo daquele item.
5. Execução posterior com valor diferente do resolvido → `reaberta`.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Excel 50 × Omie 30 | Divergência `quantidade_divergente` com 50/30 | Resolver |
| Vínculo | Nota Omie sem contrato | Divergência `evento_sem_vinculo` | Vincular na tabela de correspondência |
| Resolução | Aceitar Omie | Saldo do item = vigente − 30; histórico registrado | Desfazer com nova resolução |
| Falha | Resolver sem justificativa | Recusa | Preencher |

## Checklist de execução

- [ ] Coleção e regras por papel.
- [ ] Detecção dos seis tipos no motor.
- [ ] Tela com filtros e resolução.
- [ ] Preferência pontual aplicada ao saldo.
- [ ] Reabertura automática.

## Critérios de aceite

- [ ] **CA-F2-12-1:** o cenário Excel 50 × Omie 30 gera divergência `quantidade_divergente` com os dois valores.
- [ ] **CA-F2-12-2:** faturamento sem vínculo gera divergência `evento_sem_vinculo`.
- [ ] **CA-F2-12-3:** resolver sem justificativa é recusado; com justificativa, a resolução registra ator e data.
- [ ] **CA-F2-12-4:** `aceitar_fonte` muda o saldo daquele item e fica no histórico do contrato.
- [ ] **CA-F2-12-5:** nova execução com valor diferente do resolvido reabre a divergência.
- [ ] **CA-F2-12-6:** leitor vê a fila, mas não resolve (403).
- [ ] **CA-F2-12-7:** os filtros por tipo, estado, contrato, fonte e responsável retornam apenas os itens correspondentes.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Executar o cenário Excel 50 × Omie 30 | Nenhum registro de divergência | Consulta |
| GREEN | CA-F2-12-1 a -4 | Lotes de duas fontes divergentes; nota sem vínculo; resolver | Conforme os critérios | Capturas da fila e do detalhe |
| REFACTOR/REGRESSÃO | CA-F2-12-5 a -7 | Nova execução com outro valor; acesso como leitor; filtros | Reabertura; 403; filtros corretos | Capturas |

**Dados/fixtures:** `FX-MULTI-01` e uma nota Omie fictícia sem pedido.
**Caminhos de erro obrigatórios:** divergência sobre item removido, resolução concorrente, execução revertida.
**Evidência exigida:** capturas e histórico do contrato.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T40 | Criar a coleção `divergencias` e a detecção dos seis tipos ao fim de cada execução | Fábio Pimentel (Champion) | SPEC-F2-12 | Seis tipos detectados.<br>Valores por fonte preservados. | CA-F2-12-1 e CA-F2-12-2 | A fazer |
| F2-T41 | Construir a fila com filtros e resolução justificada, inclusive 'aceitar fonte' | Fábio Pimentel (Champion) | SPEC-F2-12 | Resolução exige justificativa e registra ator.<br>'Aceitar fonte' grava preferência pontual.<br>Leitor não resolve. | CA-F2-12-3, -4, -6 e -7 | A fazer |
| F2-T42 | Reabrir automaticamente a divergência resolvida quando a fonte trouxer valor diferente | Fábio Pimentel (Champion) | SPEC-F2-12 | Estado `reaberta` após nova execução divergente. | CA-F2-12-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
