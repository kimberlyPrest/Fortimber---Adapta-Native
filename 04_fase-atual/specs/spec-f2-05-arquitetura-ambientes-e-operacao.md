# SPEC-F2-05 — Arquitetura, ambientes e operação registrados

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** TI Fortimber
**Origem no escopo:** Escopo §7 (direção arquitetural, princípios) e §12 item 6
**Degrau da solução:** reuso — legitima o PocketBase via Skip, já em uso, e a área de dados Postgres/Python na VM; nenhum componente novo.

## Resultado observável

Existe um registro de decisão de arquitetura (ADR-001) aprovado. Há pelo menos dois ambientes separados. O backup do sistema foi restaurado com sucesso em homologação. Existe um inventário de segredos, sem valores, com dono e rotação, e um substituto nomeado para operar o sistema.

## Limites e dependências

- **Inclui:**
  - ADR-001 em `03_documentos/arquitetura/adr-001-plataforma-e-integracao.md`, cobrindo:
    - PocketBase (Skip) como base da aplicação;
    - Postgres na VM como área de dados e origem dos conectores;
    - padrão de integração: os jobs da VM empurram dados para a API do sistema com conta de serviço (SPEC-F2-09);
    - alternativas descartadas e motivo (migrar para Supabase agora; PocketBase lendo o Postgres diretamente).
  - Ambientes: desenvolvimento e produção no mínimo; homologação recomendada. Dados reais só nos ambientes autorizados.
  - Backup do PocketBase (`pb_data`): frequência, retenção, local, quem restaura e teste de restauração; RPO e RTO propostos.
  - Segredos: onde ficam (variáveis de ambiente do Skip, cofre ou arquivo protegido na VM), quem acessa, rotação e revogação.
  - Operação: responsável e substituto (mitiga o risco de uma pessoa só, Escopo §11).
- **Fora de escopo:** SSO e MFA (a TI registra a decisão; a implementação fica para outra fase); monitoramento de infraestrutura além do painel de fontes (SPEC-F2-13).
- **Entradas e pré-condições:** acesso ao painel do Skip e à VM; contrato e plano do Skip.
- **Saídas/artefatos:** ADR-001, `03_documentos/operacao/backup-e-restauracao.md`, `03_documentos/operacao/inventario-de-segredos.md` (sem valores).
- **Dependências e responsáveis:** TI Fortimber com o Champion; patrocinador ciente do custo.
- **Risco e plano B:** se o Skip não permitir backup automatizado, fazer exportação agendada do `pb_data` a partir da VM ou snapshot manual semanal, com o risco aceito por escrito.
- **Rollback ou reversão:** ADR substituível por ADR nova; ambientes não são destruídos sem backup.

## Fluxo e regras

1. A TI redige a ADR-001 a partir desta SPEC e do sistema já construído; o patrocinador aprova.
2. A TI configura os ambientes e documenta URL, finalidade e quem acessa cada um.
3. A TI executa backup e restauração em homologação e confere contagens.
4. A TI inventaria cada segredo (nome, onde vive, dono, rotação, como revogar) **sem registrar o valor**.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Backup de produção restaurado em homologação | Contagens de contratos, itens e eventos iguais | — |
| Limite | Skip sem backup nativo | Plano B documentado e risco aceito | Rever trimestralmente |
| Falha | Restauração falha | Incidente registrado; backup refeito antes de qualquer conector entrar em produção | Bloqueia SPEC-F2-10 em produção |

## Checklist de execução

- [ ] ADR-001 escrita e aprovada.
- [ ] Ambientes documentados.
- [ ] Backup e restauração testados.
- [ ] Inventário de segredos sem valores.
- [ ] Responsável e substituto nomeados.

## Critérios de aceite

- [ ] **CA-F2-05-1:** ADR-001 aprovada, com nome do aprovador e data.
- [ ] **CA-F2-05-2:** restauração executada em homologação com as contagens de contratos, itens e eventos iguais às da origem do backup.
- [ ] **CA-F2-05-3:** inventário de segredos com nome, local, dono, rotação e revogação para cada segredo, sem nenhum valor.
- [ ] **CA-F2-05-4:** pelo menos dois ambientes separados; dados reais apenas onde autorizado.
- [ ] **CA-F2-05-5:** substituto operacional nomeado e com acesso testado.

## TDD da SPEC

Entrega majoritariamente operacional: roteiro de verificação.

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Procurar a decisão de arquitetura e o backup | Inexistentes | Nota no check |
| GREEN | CA-F2-05-1 a -4 | Restaurar o backup em homologação; conferir as contagens | Contagens iguais | Registro da restauração |
| REFACTOR/REGRESSÃO | CA-F2-05-5 | O substituto faz login e executa uma restauração assistida | Sucesso | Registro assinado |

**Dados/fixtures:** backup de produção ou da base com a amostra.
**Caminhos de erro obrigatórios:** restauração parcial; segredo sem dono; ambiente compartilhado.
**Evidência exigida:** documentos no repo do cliente e registro de restauração.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T15 | Redigir e aprovar a ADR-001 (plataforma e padrão de integração) e documentar os ambientes | TI Fortimber | SPEC-F2-05 | ADR aprovada, com nome e data.<br>Ambientes com URL, finalidade e acesso. | CA-F2-05-1 e CA-F2-05-4 | A fazer |
| F2-T16 | Definir backup do PocketBase e executar uma restauração em homologação | TI Fortimber | SPEC-F2-05 | Frequência, retenção e local definidos.<br>Restauração com contagens iguais. | CA-F2-05-2 | A fazer |
| F2-T17 | Inventariar segredos sem valores e nomear o substituto operacional | TI Fortimber | SPEC-F2-05 | Cada segredo com local, dono, rotação e revogação.<br>Substituto com acesso testado. | CA-F2-05-3 e CA-F2-05-5 | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
