# Fase 2 — Integrações e reconciliação das fontes prioritárias

**Resultado:** contratos e entregas das fontes autorizadas (área de dados Postgres e Omie) entram no sistema de forma repetível, versionada e sem dupla contagem. Cada fonte mostra último sucesso, atraso e falhas. As divergências entre fontes ficam numa fila com resolução humana justificada. A página inicial mostra indicadores reais com numerador e denominador. As fontes ainda não integradas aparecem como lacuna, sem contaminar os números.

**Antes das integrações (Onda 0):** a fase começa estabilizando o que a Fase 1 entregou. Isso inclui saldo sem dupla contagem, Visão Geral sem dados fictícios, acesso endurecido, regras de negócio e arquitetura aprovadas e o UAT da Fase 1 com amostra real. Nenhum conector entra em produção antes de a Onda 0 terminar.

**SPECs:** ver [índice de SPECs](specs/00-INDICE.md).

## Ondas

| Onda | Objetivo | SPECs |
|---|---|---|
| 0 — Estabilização | Corrigir e aprovar a base da Fase 1 | F2-01, F2-02, F2-03, F2-04, F2-05, F2-06 |
| 1 — Fundação de integração | Fontes cadastradas, sincronização versionada e conta de serviço | F2-07, F2-08, F2-09 |
| 2 — Conectores | Contratos da área de dados e faturamento do Omie | F2-10, F2-11 |
| 3 — Reconciliação, visibilidade e aceite | Divergências, saúde das fontes, indicadores, inventário e UAT | F2-12, F2-13, F2-14, F2-15, F2-16 |

## Regra de execução

- As tasks estão agrupadas em **levas** (A a G). Tasks da mesma leva não dependem umas das outras e podem andar em paralelo; uma leva só começa quando as dependências de cada task estiverem concluídas.
- Toda task fecha com a evidência indicada no "Recorte da prova" da SPEC.
- Testes automatizados ficam a critério do time da Fortimber; o registro deles não é exigido. A prova de aceite é o cenário demonstrado.
- Os sistemas de origem continuam somente leitura. Nenhum envio externo (e-mail, WhatsApp) e nenhuma escrita em Omie, Postgres de origem, Siscomex, porto, SISAP ou Dolphin.
- Decisão pendente não é inventada: enquanto não for aprovada, vale a premissa marcada na tela como "premissa não aprovada", e o indicador que depende dela aparece como "aguardando definição".

## Aceite da fase (escopo §8)

- [ ] Execução pode ser repetida sem duplicar contratos ou eventos.
- [ ] Estado e horário de cada fonte aparecem no sistema.
- [ ] Falha de fonte gera sinal visível.
- [ ] Amostra de cada fonte integrada reconcilia com a origem.
- [ ] Fonte ainda não integrada aparece como lacuna e não contamina as métricas.
- [ ] Correções da Fase 1 (Onda 0) aceitas e UAT da Fase 1 com amostra real registrado.

## Fora desta fase

- Importador do Sysflora/SISFLORA e dados de origem e cadeia de custódia — Fase 3.
- Conectores para Siscomex, sistema do porto, SISAP e Dolphin — nesta fase só o inventário de viabilidade (SPEC-F2-15); construção na Fase 3, conforme decisão.
- Produção, estoque, reserva e disponibilidade futura — Fase 3.
- Alertas de prazo e de risco de contrato; fila de exceções operacionais — Fases 3 e 4.
- Agentes, loops e linguagem natural — Fase 4.
- Notificações externas a clientes (e-mail, WhatsApp) — fora até aprovação específica.
- Sincronização bidirecional ou qualquer escrita em sistema de origem — fora do programa.
- Descartar o Excel como fonte antes do aceite formal da reconciliação — fora desta fase.
- Margem e resultado financeiro por contrato — fora até definição financeira aprovada.
- SSO, MFA e segregação por filial — a TI registra a decisão nesta fase; a implementação fica para fase posterior, salvo decisão.

## Tasks

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Dependências | Leva | Status | Tipo |
|---|---|---|---|---|---|---|---|---|---|
| F2-T01 | Reescrever o cálculo de saldo com total por fonte e recálculo em criação, alteração e remoção de evento | Fábio Pimentel (Champion) | SPEC-F2-01 | Hook recalcula nos três gatilhos.<br>Fontes iguais contam uma vez; diferentes viram `em_divergencia`.<br>Campos `fontes_resumo` e `divergencia_fontes` gravados. | CA-F2-01-1, -2, -3 e -5 (GREEN e REGRESSÃO) | — | A | A fazer | Correção |
| F2-T02 | Exibir no detalhe do contrato a entrega por fonte e o estado de concordância | Fábio Pimentel (Champion) | SPEC-F2-01 | Bloco 'Entregue por fonte' com total de cada fonte.<br>Indicação concorda/diverge visível. | Captura do CA-F2-01-2 (totais 50/30 visíveis) | — | A | A fazer | Correção |
| F2-T03 | Aplicar a fonte preferencial vigente (e a preferência pontual por item) no cálculo do saldo | Fábio Pimentel (Champion) | SPEC-F2-01 | Com preferência vigente, saldo segue a preferencial.<br>Aviso de divergência com as demais fontes. | CA-F2-01-4 (REFACTOR/REGRESSÃO) | F2-T01, F2-T22 | C | A fazer | Implementação |
| F2-T04 | Remover perfil, notificação e dados fictícios do cabeçalho, menu e sino; mostrar o usuário autenticado | Fábio Pimentel (Champion) | SPEC-F2-02 | Cabeçalho e menu com nome e papel reais.<br>Sino sem item fictício.<br>Nenhum import de mock na interface. | CA-F2-02-1 e CA-F2-02-4 | — | A | A fazer | Correção |
| F2-T05 | Trocar a Visão Geral por contagens reais por estado de saldo e última carga, com estado vazio | Fábio Pimentel (Champion) | SPEC-F2-02 | Contagens iguais às da lista filtrada.<br>Base vazia mostra mensagem.<br>Filtro de período funcional ou removido. | CA-F2-02-2, -3 e -5 | — | A | A fazer | Correção |
| F2-T06 | Fechar criação e promoção de usuários para admin e neutralizar a promoção automática da conta compartilhada | Fábio Pimentel (Champion) | SPEC-F2-03 | `users.createRule` só admin/superuser.<br>Migração nova neutraliza a 0007.<br>Conta compartilhada rebaixada ou removida. | CA-F2-03-1 e a parte de código do CA-F2-03-5 | — | A | A fazer | Segurança |
| F2-T07 | Aplicar a leitura por papel em proveniência, cargas e UAT no servidor e ocultar na interface | Fábio Pimentel (Champion) | SPEC-F2-03 | Leitor sem acesso a `proveniencia_carregamentos` e `cargas`.<br>`uat_execucoes` sem escrita direta.<br>Menu e telas ocultam o que o papel não acessa. | CA-F2-03-2 e CA-F2-03-6 | — | A | A fazer | Segurança |
| F2-T08 | Exportar a ata da UAT por requisição autenticada, sem credencial na URL | Fábio Pimentel (Champion) | SPEC-F2-03 | Download por requisição com cabeçalho de autorização.<br>Endpoint recusa chamada sem cabeçalho. | CA-F2-03-3 | — | A | A fazer | Segurança |
| F2-T09 | Ligar cada resposta de UAT ao usuário logado e ao seu papel de UAT | Fábio Pimentel (Champion) | SPEC-F2-03 | Campos `papel_uat` e `respondido_por` criados.<br>Hook recusa papel diferente do usuário. | CA-F2-03-4 | — | A | A fazer | Segurança |
| F2-T10 | Criar a tela de login única com guarda de rota e remover os logins internos das páginas | Fábio Pimentel (Champion) | SPEC-F2-03 | Rotas protegidas redirecionam para `/login`.<br>Nenhum e-mail pré-preenchido no código. | CA-F2-03-5 | — | A | A fazer | Segurança |
| F2-T11 | Selecionar a amostra real de 20 a 30 contratos e registrá-la por código e categoria | Comercial Fortimber | SPEC-F2-04 | Composição mínima atendida ou justificada.<br>Sem dados pessoais no documento. | CA-F2-04-2 | — | A | A fazer | Decisão funcional |
| F2-T12 | Aprovar D-F1-01 (contrato ativo), D-F1-02 (marco de entrega e faturamento) e D-F1-03 (aditivo e excedente) | Patrocinador Fortimber | SPEC-F2-04 | Três decisões com resposta, aprovador e data.<br>Diferenças em relação à premissa listadas com a SPEC afetada. | CA-F2-04-1 (D-F1-01 a 03) e CA-F2-04-3 | — | A | A fazer | Decisão funcional |
| F2-T13 | Aprovar D-F1-04 (unidades e conversões) e D-F1-06 (fonte preferencial e tolerância por campo) | Comercial Fortimber | SPEC-F2-04 | Unidades por produto registradas.<br>Matriz por campo pronta para cadastro. | CA-F2-04-1 (D-F1-04 e 06) e CA-F2-04-4 | — | A | A fazer | Decisão funcional |
| F2-T14 | Aprovar D-F1-07 (universo, período e denominador da cobertura e da rastreabilidade) | Patrocinador Fortimber | SPEC-F2-04 | Universo, período, unidade e vínculo válido definidos. | CA-F2-04-1 (D-F1-07) | — | A | A fazer | Decisão funcional |
| F2-T15 | Redigir e aprovar a ADR-001 (plataforma e padrão de integração) e documentar os ambientes | TI Fortimber | SPEC-F2-05 | ADR aprovada, com nome e data.<br>Ambientes com URL, finalidade e acesso. | CA-F2-05-1 e CA-F2-05-4 | — | A | A fazer | Decisão técnica |
| F2-T16 | Definir backup do PocketBase e executar uma restauração em homologação | TI Fortimber | SPEC-F2-05 | Frequência, retenção e local definidos.<br>Restauração com contagens iguais. | CA-F2-05-2 | — | A | A fazer | Operação |
| F2-T17 | Inventariar segredos sem valores e nomear o substituto operacional | TI Fortimber | SPEC-F2-05 | Cada segredo com local, dono, rotação e revogação.<br>Substituto com acesso testado. | CA-F2-05-3 e CA-F2-05-5 | — | A | A fazer | Segurança |
| F2-T18 | Gerar o CSV da amostra real a partir da área de dados e publicá-lo pela carga atual | Fábio Pimentel (Champion) | SPEC-F2-06 | Pré-validação e publicação concluídas.<br>Toda rejeição explicada. | CA-F2-06-1 | F2-T01, F2-T06, F2-T11 | B | A fazer | Implementação |
| F2-T19 | Executar os oito cenários de UAT da Fase 1 com contas individuais e planilha esperado × observado | Comercial Fortimber | SPEC-F2-06 | Oito cenários respondidos com `respondido_por`.<br>Planilha assinada.<br>Cenários sem suporte marcados como bloqueados. | CA-F2-06-2, -3 e -5 | F2-T09, F2-T10, F2-T18 | C | A fazer | Aceite humano |
| F2-T20 | Registrar a decisão de piloto da Fase 1 e exportar a ata para 05_entregas | Patrocinador Fortimber | SPEC-F2-06 | Decisão em `uat_decisoes`.<br>Ata em `05_entregas/fase-1/`. | CA-F2-06-4 | F2-T19 | D | A fazer | Aceite humano |
| F2-T21 | Criar a coleção `fontes`, cadastrar as nove fontes e publicar a tela Fontes (leitura) | Fábio Pimentel (Champion) | SPEC-F2-07 | Nove fontes com situação, dono e frequência.<br>Leitor só lê. | CA-F2-07-1 e CA-F2-07-2 (parte de fontes) | — | A | A fazer | Implementação |
| F2-T22 | Criar `preferencias_campo` com vigência, aprovador obrigatório e edição só por admin | Fábio Pimentel (Champion) | SPEC-F2-07 | Recusa sem aprovador ou data.<br>Nova vigência fecha a anterior. | CA-F2-07-2 (parte de preferências), CA-F2-07-3 e -4 | F2-T21 | B | A fazer | Implementação |
| F2-T23 | Cadastrar no sistema a matriz D-F1-06 aprovada | Comercial Fortimber | SPEC-F2-07 | Uma preferência vigente por campo da matriz. | CA-F2-07-5 | F2-T13, F2-T22 | C | A fazer | Configuração |
| F2-T24 | Criar `execucoes_fonte` e registrar toda carga com contagens, checksum e `extraido_em` | Fábio Pimentel (Champion) | SPEC-F2-08 | Execução aberta e fechada com contagens e erros.<br>Mesmo checksum encerra com 0 alterações. | CA-F2-08-1 e CA-F2-08-6 (registro da falha) | — | A | A fazer | Implementação |
| F2-T25 | Implementar o upsert por (`sistema_origem`, `id_externo`) com revisões e eventos para contratos já existentes | Fábio Pimentel (Champion) | SPEC-F2-08 | Aditivo cria revisão e preserva a anterior.<br>Revisão retroativa não sobrescreve.<br>Evento avulso é vinculado. | CA-F2-08-3 e CA-F2-08-4 | F2-T24 | B | A fazer | Implementação |
| F2-T26 | Versionar alterações de campo em contrato e item e recalcular o saldo | Fábio Pimentel (Champion) | SPEC-F2-08 | Antes e depois gravados em `versoes_registro`.<br>Histórico visível no detalhe.<br>Saldo recalculado. | CA-F2-08-2 | F2-T01, F2-T25 | C | A fazer | Implementação |
| F2-T27 | Implementar o rollback por execução com restauração de versões e bloqueio fora de ordem | Fábio Pimentel (Champion) | SPEC-F2-08 | Reversão restaura valores e remove eventos da execução.<br>Recusa se houver execução posterior conflitante. | CA-F2-08-5 e cenário Bloqueio | F2-T25 | C | A fazer | Implementação |
| F2-T28 | Migrar a carga CSV da tela de Cargas para o motor versionado | Fábio Pimentel (Champion) | SPEC-F2-08 | Tela de Cargas usa o motor.<br>Cenários de carga da Fase 1 sem regressão. | CA-F2-08-7 e CA-F2-08-8 | F2-T25, F2-T27 | D | A fazer | Implementação |
| F2-T29 | Criar a coleção `contas_servico`, uma conta por fonte, sem acesso à interface nem a outras coleções | Fábio Pimentel (Champion) | SPEC-F2-09 | Contas das fontes Postgres e Omie criadas.<br>Listagem de `users` e `contratos` negada. | CA-F2-09-3 | F2-T06, F2-T21 | B | A fazer | Segurança |
| F2-T30 | Criar o endpoint de ingestão com validação de esquema, limites, restrição por fonte e logs sem conteúdo | Fábio Pimentel (Champion) | SPEC-F2-09 | Lote válido vira execução.<br>Fonte errada recebe 403; lote inválido recebe 400.<br>Contrato do payload documentado. | CA-F2-09-1, -2, -4 e -6 | F2-T25, F2-T29 | C | A fazer | Implementação |
| F2-T31 | Documentar e testar a rotação e a revogação das contas de serviço | TI Fortimber | SPEC-F2-09 | Procedimento no inventário de segredos.<br>Conta revogada recebe 401. | CA-F2-09-5 | F2-T17, F2-T29 | C | A fazer | Segurança |
| F2-T32 | Criar o usuário de banco somente leitura para o conector na área de dados | TI Fortimber | SPEC-F2-10 | INSERT, UPDATE e DELETE recusados.<br>Acesso só às views do conector. | CA-F2-10-1 | — | A | A fazer | Segurança |
| F2-T33 | Criar as views canônicas de contratos e itens e publicar o mapeamento campo a campo | Fábio Pimentel (Champion) | SPEC-F2-10 | Views no esquema canônico.<br>Mapeamento com transformação e unidade. | CA-F2-10-6 | F2-T12, F2-T32 | B | A fazer | Implementação |
| F2-T34 | Construir e agendar o job Python que envia contratos à ingestão, com novas tentativas e log | Fábio Pimentel (Champion) | SPEC-F2-10 | Primeira execução carrega o universo; segunda, 0 alterações.<br>Alteração aparece com versão.<br>Falha tenta de novo e fica visível. | CA-F2-10-2, -3 e -4 | F2-T30, F2-T33 | D | A fazer | Integração |
| F2-T35 | Reconciliar 30 contratos da amostra entre o sistema e o Postgres | Comercial Fortimber | SPEC-F2-10 | Planilha esperado × observado assinada.<br>Diferenças classificadas. | CA-F2-10-5 | F2-T34 | E | A fazer | Aceite humano |
| F2-T36 | Inventariar os endpoints Omie consumidos e a chave pedido ↔ contrato | Fábio Pimentel (Champion) | SPEC-F2-11 | Endpoints, limites, paginação, campos e chave documentados. | CA-F2-11-1 | — | A | A fazer | Descoberta técnica |
| F2-T37 | Adicionar o evento `faturamento` e o parâmetro de contagem definido pela D-F1-02 | Fábio Pimentel (Champion) | SPEC-F2-11 | Tipo novo aceito pelo modelo.<br>Saldo respeita o parâmetro. | CA-F2-11-4 | F2-T01, F2-T12 | B | A fazer | Implementação |
| F2-T38 | Construir o job Omie que envia faturamentos vinculados e manda os sem vínculo para a fila | Fábio Pimentel (Champion) | SPEC-F2-11 | Nota vinculada aparece no item.<br>Sem vínculo vira divergência.<br>Reexecução não duplica. | CA-F2-11-2, -3 e -5 | F2-T30, F2-T36, F2-T37, F2-T40 | D | A fazer | Integração |
| F2-T39 | Reconciliar a quantidade faturada de 20 contratos entre o sistema e o Omie | Financeiro Fortimber | SPEC-F2-11 | Planilha assinada.<br>Diferenças classificadas. | CA-F2-11-6 | F2-T38 | E | A fazer | Aceite humano |
| F2-T40 | Criar a coleção `divergencias` e a detecção dos seis tipos ao fim de cada execução | Fábio Pimentel (Champion) | SPEC-F2-12 | Seis tipos detectados.<br>Valores por fonte preservados. | CA-F2-12-1 e CA-F2-12-2 | F2-T25 | C | A fazer | Implementação |
| F2-T41 | Construir a fila com filtros e resolução justificada, inclusive 'aceitar fonte' | Fábio Pimentel (Champion) | SPEC-F2-12 | Resolução exige justificativa e registra ator.<br>'Aceitar fonte' grava preferência pontual.<br>Leitor não resolve. | CA-F2-12-3, -4, -6 e -7 | F2-T40 | D | A fazer | Implementação |
| F2-T42 | Reabrir automaticamente a divergência resolvida quando a fonte trouxer valor diferente | Fábio Pimentel (Champion) | SPEC-F2-12 | Estado `reaberta` após nova execução divergente. | CA-F2-12-5 | F2-T40 | D | A fazer | Implementação |
| F2-T43 | Construir a tela de saúde das fontes com estado, último sucesso e falha, contagens e atraso parametrizado | Fábio Pimentel (Champion) | SPEC-F2-13 | Estados ok, atrasada, com falha, lacuna e sem frequência.<br>Regra de atraso pela frequência acordada. | CA-F2-13-1, -3 (tela) e -5 | F2-T21, F2-T24 | B | A fazer | Implementação |
| F2-T44 | Separar 'extraído da fonte em' de 'carregado em' no contrato e avisar fonte atrasada | Fábio Pimentel (Champion) | SPEC-F2-13 | Duas datas no detalhe.<br>Limite fixo de 7 dias removido.<br>Aviso nos contratos da fonte atrasada. | CA-F2-13-3 (detalhe) e CA-F2-13-4 | F2-T21, F2-T24 | B | A fazer | Implementação |
| F2-T45 | Ligar o sino a falhas reais de execução e a fontes atrasadas | Fábio Pimentel (Champion) | SPEC-F2-13 | Falha aparece no sino de operador e admin em até 1 ciclo. | CA-F2-13-2 | F2-T04, F2-T24 | B | A fazer | Implementação |
| F2-T46 | Publicar o indicador de contratos ativos com saldo (n/N), com fórmula, lista e estado pendente | Fábio Pimentel (Champion) | SPEC-F2-14 | n/N com fórmula e lista clicável.<br>Sem D-F1-07, mostra 'aguardando definição'. | CA-F2-14-1, -4 e -5 (n/N) | F2-T05, F2-T12, F2-T14 | B | A fazer | Implementação |
| F2-T47 | Publicar contratado × entregue por unidade e período, sem misturar unidades | Fábio Pimentel (Champion) | SPEC-F2-14 | Totais separados por unidade.<br>Filtro de período funcional. | CA-F2-14-2 e -5 (totais) | F2-T05, F2-T13 | B | A fazer | Implementação |
| F2-T48 | Publicar a cobertura por fonte com lista de contratos sem dado e lacunas | Fábio Pimentel (Champion) | SPEC-F2-14 | Proporção por fonte.<br>Fonte não integrada como lacuna. | CA-F2-14-3 e -5 (cobertura) | F2-T05, F2-T21 | B | A fazer | Implementação |
| F2-T49 | Preencher a ficha de viabilidade do Siscomex | Fábio Pimentel (Champion) | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (Siscomex) | — | A | A fazer | Descoberta técnica |
| F2-T50 | Preencher a ficha de viabilidade do sistema do porto | TI Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (porto) | — | A | A fazer | Descoberta técnica |
| F2-T51 | Preencher a ficha de viabilidade do SISAP (cópia somente leitura com o fornecedor) | TI Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (SISAP) | — | A | A fazer | Descoberta técnica |
| F2-T52 | Preencher a ficha de viabilidade do Dolphin | Produção Fortimber | SPEC-F2-15 | Ficha completa ou 'sem resposta' com data.<br>Sem credenciais. | CA-F2-15-1, -2 e -4 (Dolphin) | — | A | A fazer | Descoberta técnica |
| F2-T53 | Consolidar as fichas e registrar a decisão integrar, adiar ou descartar para a Fase 3 | Consultora Adapta (Kimberly Prestes) | SPEC-F2-15 | Decisão do patrocinador registrada com prioridade.<br>Situação atualizada na tela Fontes. | CA-F2-15-3 | F2-T21, F2-T49, F2-T50, F2-T51, F2-T52 | B | A fazer | Decisão funcional |
| F2-T54 | Redigir o roteiro de UAT da Fase 2, com 12 ou mais cenários ligados aos CAs | Consultora Adapta (Kimberly Prestes) | SPEC-F2-16 | Cada cenário aponta para CAs das SPECs F2-01 a F2-14. | CA-F2-16-1 | — | A | A fazer | Aceite humano |
| F2-T55 | Executar o roteiro de UAT da Fase 2 com contas individuais | Comercial Fortimber | SPEC-F2-16 | Cada cenário com esperado, observado, evidência e `respondido_por`.<br>Aceite binário do escopo §8 marcado item a item. | CA-F2-16-2 e CA-F2-16-3 | F2-T03, F2-T20, F2-T23, F2-T26, F2-T28, F2-T31, F2-T35, F2-T39, F2-T41, F2-T42, F2-T43, F2-T44, F2-T45, F2-T46, F2-T47, F2-T48, F2-T53, F2-T54 | F | A fazer | Aceite humano |
| F2-T56 | Registrar a decisão de aceite da Fase 2 e as ressalvas com dono e prazo | Patrocinador Fortimber | SPEC-F2-16 | Decisão registrada e ata em `05_entregas/fase-2/`.<br>Ressalvas com dono e prazo. | CA-F2-16-4 e CA-F2-16-5 | F2-T55 | G | A fazer | Aceite humano |
