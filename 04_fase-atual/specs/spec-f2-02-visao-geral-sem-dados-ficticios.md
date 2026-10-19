# SPEC-F2-02 — Visão Geral sem dados fictícios

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** Fábio Pimentel (Champion)
**Origem no escopo:** Escopo §6 regra 4 e princípio "nunca apresentar estimado como confirmado"
**Degrau da solução:** reuso — usa `src/services/contratos.ts` e o componente `KpiCard` existentes; remove os mocks de `src/data/dashboard.ts`.

## Resultado observável

A página inicial só exibe números calculados a partir dos dados carregados, cada um com origem e "atualizado em". Nenhum nome, perfil, notificação, gráfico ou contrato fictício aparece em nenhuma tela.

## Limites e dependências

- **Inclui:**
  - parar de usar `DASHBOARD_KPIS`, `MONTHLY_VOLUME_DATA`, `CONTRACTS_DATA`, `USER_PROFILE` e `SAMPLE_NOTIFICATION`;
  - cabeçalho e menu lateral com o nome e o papel do usuário autenticado;
  - Visão Geral provisória com: total de contratos carregados, contagem por estado de saldo (calculado, não calculável, em divergência, sem eventos, cancelado), data e hora da última carga publicada e atalho para a lista filtrada.
- **Fora de escopo:** indicadores de negócio (n/N de contratos ativos com saldo, contratado × entregue, cobertura) — SPEC-F2-14; alertas reais no sino — SPEC-F2-13.
- **Entradas e pré-condições:** sessão autenticada; coleções `contratos`, `contratos_itens`, `cargas`.
- **Saídas/artefatos:** `Index.tsx`, `Header.tsx`, `Sidebar.tsx` e `NotificationsPopover.tsx` revisados; `dashboard.ts` reduzido à configuração de navegação.
- **Dependências e responsáveis:** nenhuma; pode começar imediatamente.
- **Risco e plano B:** se a base estiver vazia, a tela mostra "nenhum dado carregado ainda", nunca zeros fictícios.
- **Rollback ou reversão:** reverter o commit; não há migração de dados.

## Fluxo e regras

1. Ao abrir `/`, consultar as contagens reais na base.
2. Cada cartão mostra valor, rótulo, origem ("carga X publicada em …") e "atualizado em".
3. O filtro de período só aparece se alterar os números; caso contrário, é removido.
4. Itens de menu desabilitados (Planejamento, Produção, Estoque etc.) continuam desabilitados, sem números.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Base com a amostra carregada | Contagens por estado de saldo iguais às da lista de contratos | — |
| Limite | Base vazia | Mensagem "nenhum dado carregado ainda" | — |
| Falha | API indisponível | Mensagem de erro e opção de tentar novamente; nenhum número exibido | Tentar novamente |

## Checklist de execução

- [ ] Nenhum componente importa dados fictícios de `dashboard.ts`.
- [ ] Cabeçalho e menu mostram o usuário real.
- [ ] Visão Geral provisória ligada à base.
- [ ] Filtro de período funcional ou removido.

## Critérios de aceite

- [ ] **CA-F2-02-1:** uma busca no código por `DASHBOARD_KPIS`, `MONTHLY_VOLUME_DATA`, `CONTRACTS_DATA`, `USER_PROFILE` e `SAMPLE_NOTIFICATION` não encontra nenhum uso na interface.
- [ ] **CA-F2-02-2:** com a base vazia, a Visão Geral mostra "nenhum dado carregado ainda" e nenhum valor numérico.
- [ ] **CA-F2-02-3:** as contagens por estado de saldo são iguais às da lista de Contratos filtrada pelo mesmo estado.
- [ ] **CA-F2-02-4:** o cabeçalho mostra o nome e o papel do usuário logado; sem sessão, não mostra perfil.
- [ ] **CA-F2-02-5:** o filtro de período não existe ou altera os números exibidos.

## TDD da SPEC

Prova por cenário demonstrado; registro de testes automatizados não exigido (decisão de 19/10/2026).

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Base vazia no sistema atual | Abrir `/` | KPIs e contratos fictícios aparecem | Captura |
| GREEN | CA-F2-02-2, -3 e -4 | Abrir `/` vazia; carregar a amostra; comparar com a lista | Mensagem de vazio; contagens iguais; usuário real | Capturas lado a lado |
| REFACTOR/REGRESSÃO | CA-F2-02-1 e -5 | `grep -rn "DASHBOARD_KPIS\|CONTRACTS_DATA\|USER_PROFILE\|SAMPLE_NOTIFICATION" src` | Nenhum uso na interface | Saída do comando |

**Dados/fixtures:** fixtures da Fase 1 (`fixture-piloto`).
**Caminhos de erro obrigatórios:** base vazia, API indisponível, sessão expirada.
**Evidência exigida:** capturas e saída do grep anexadas à task.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T04 | Remover perfil, notificação e dados fictícios do cabeçalho, menu e sino; mostrar o usuário autenticado | Fábio Pimentel (Champion) | SPEC-F2-02 | Cabeçalho e menu com nome e papel reais.<br>Sino sem item fictício.<br>Nenhum import de mock na interface. | CA-F2-02-1 e CA-F2-02-4 | A fazer |
| F2-T05 | Trocar a Visão Geral por contagens reais por estado de saldo e última carga, com estado vazio | Fábio Pimentel (Champion) | SPEC-F2-02 | Contagens iguais às da lista filtrada.<br>Base vazia mostra mensagem.<br>Filtro de período funcional ou removido. | CA-F2-02-2, -3 e -5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
