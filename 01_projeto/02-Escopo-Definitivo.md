# Escopo definitivo — Planejamento integrado da produção e dos contratos

**Cliente:** Fortimber Indústria Florestal Ltda.
**Versão:** 1.0 — consolidação documental para validação
**Data:** 12/10/2026
**Horizonte de acompanhamento citado no kick-off:** quatro meses
**Champion técnico identificado:** Fábio Pimentel
**Patrocinadores de negócio identificados:** Vinícius Belluzzo e Fernanda Belluzzo

> Este documento consolida o recorte de negócio acordado e transforma as oportunidades das reuniões em um plano executável de cinco fases. Onde os arquivos não definem regras, fontes, permissões ou metas numéricas, o item está identificado como premissa ou decisão pendente; não deve ser tratado como comportamento já aprovado. O escopo não equivale a uma promessa de software entregue pela Adapta: a construção é conjunta, com a Fortimber responsável pelo código e operação, apoiada por consultoria e capacitação Adapta Native.

## 1. Síntese executiva

A Fortimber precisa responder com rapidez e confiança, para cada contrato ou pedido de venda: o que foi prometido, quanto já foi produzido e entregue, quanto continua pendente, de onde virá a madeira/produto, quando poderá estar disponível e quais riscos exigem decisão. Hoje essas respostas dependem de pessoas consultando planilhas, Power BI e sistemas de produção, financeiros e de exportação que não compartilham uma visão única.

O projeto estabelecerá, em incrementos, uma aplicação interna de planejamento e controle que conecte contratos e pedidos a produção, estoque, disponibilidade, expedição e faturamento, preservando a proveniência dos dados e destacando lacunas e divergências. A primeira entrega deve permitir consultar e acompanhar contratos ativos com saldo e seus volumes de produção/entrega, sem aguardar a integração perfeita de todas as fontes. As fases posteriores ampliam cobertura de dados, planejamento e recomendações. Agentes e loops são uma camada adicional nas fases 4 e 5, sujeitos a métricas, permissões e aprovação antes de qualquer ação externa.

### Resultado de negócio pretendido

Reduzir o esforço de consolidação manual e melhorar as decisões de produção e venda, contribuindo para eficiência operacional, controle do fluxo de caixa e margem por meio de melhor utilização e rastreabilidade da madeira. A prioridade expressa pelos sócios é melhorar processos e agregar valor/rastreabilidade, não aumentar a aquisição de clientes como objetivo deste projeto.

### Objetivo operacional

Disponibilizar uma visão integrada e verificável do ciclo:

**madeira/origem disponível → demanda comercial → contrato/pedido → necessidade de produção → produção realizada → estoque/reserva → expedição/entrega → saldo contratual e faturamento.**

## 2. Base de evidências e leitura das reuniões

| Evidência | O que sustenta no escopo | Limites da evidência |
|---|---|---|
| Kick-off, 17/08/2026: ata, transcrição, decisões e fluxos | Objetivo integrado; dificuldade de consultar 2–4 pessoas/sistemas para status; vendas nacionais e internacionais; fluxo de madeira e contratos; rastreabilidade como diferencial; métricas operacionais iniciais; mapeamento de processo como próximo passo. | Métricas e 95% foram apresentadas como critérios iniciais/sugestão aceita para começar, sem baseline ou fórmula detalhada. A ata registra volumes produzidos, entregues e disponibilidade futura, mas não valores-alvo para esses indicadores. |
| Reunião técnica de setup, 16/09/2026: transcrição colada pelo usuário | Fábio já mantém área de dados em VM e trabalha com Python/Postgres; cerca de 4.500 linhas de contratos extraídas em uma consulta; Omie tem API; Siscomex e sistema portuário têm APIs ainda não estudadas; Sysflora não tem API conhecida; acesso de leitura a cópia do banco SISAP ainda estava em negociação; login de portal governamental pode envolver senha rotativa mensal; comunicação corporativa principalmente por e-mail/Gmail. Também foram discutidos Skip, Maestro, Supabase, GitHub, IDE e MCPs. | As ferramentas de construção/IA e arquitetura descritas pela consultora são orientação/proposta, não uma decisão técnica de produção registrada. Integração, credenciais, permissões, licença, hospedagem e segurança precisam ser confirmadas antes de especificação técnica. |
| DMO de 10/08/2026 | Negócio verticalizado (manejo, indústria, beneficiamento e vendas nacionais/internacionais); alvo declarado de explorar cerca de 2.500 ha/ano e consumir/produzir a madeira do plano no mesmo ano; áreas Financeiro e Comercial; necessidade de integração; mercado instável e busca de eficiência. | DMO é diagnóstico, não regra de cálculo ou compromisso de meta do sistema. |
| Roteiro PDF de mapeamento | Três processos a detalhar: contrato/pedido → programação; produção → estoque → disponibilidade; acompanhamento de contratos. | O roteiro não é o mapeamento realizado. O arquivo `01-Controle de Produção.md` está vazio além do marcador de preenchimento. Não há vídeos de processo nos materiais encontrados. |
| Briefing PDF | Corrobora falta de visão conectada entre disponibilidade, produção, contratos e vendas; sugere risco econômico por volatilidade e exposição de contratos. | Os números e interpretações devem ser confirmados contra dados reais e com a liderança. |

### Decisões explicitamente registradas

1. O objetivo inicial é planejamento e controle integrados da produção e dos contratos de venda.
2. O escopo deve começar por gargalos priorizados e entregar valor incremental, em vez de tentar substituir todos os sistemas de uma vez.
3. Os indicadores iniciais são contratos ativos com saldo, volume produzido, volume entregue e disponibilidade futura.
4. A referência inicial é atingir rastreabilidade de pelo menos 95% da produção/estoque até contrato/pedido correspondente.
5. A solução será construída em conjunto; propriedade do que for desenvolvido permanece com a Fortimber, conforme alinhamento do kick-off.
6. WhatsApp foi definido como canal oficial de comunicação do projeto; isso não implica usar WhatsApp para comunicação automatizada com clientes.

### Oportunidades citadas, ainda não aprovadas como entregas

Envio automático de atualização de contrato por e-mail/WhatsApp, CRM/funil comercial, agente de voz ou chat para consultas, web scraping do Sysflora/portal governamental, substituição dos sistemas de terceiros e comercialização do sistema para outras empresas foram discutidos ou sugeridos. Não são promessa deste escopo até que tenham fonte de dados, regra, responsável, autorização e critério de aceite definidos.

## 3. Problema, usuários e valor

### Problema central

Informações economicamente relacionadas estão espalhadas por sistemas e controles paralelos. A empresa não consegue visualizar de forma consolidada a disponibilidade por produto, os compromissos comerciais, a produção em curso, as entregas e o resultado esperado. Para responder sobre um contrato, a liderança precisa pedir dados a várias pessoas. Isso atrasa decisões e torna mais difícil antecipar falta de produto, atraso, estoque sem destinação ou compromisso incompatível com a capacidade produtiva.

### Usuários e responsabilidades previstas

| Papel | Necessidade principal | Participação esperada |
|---|---|---|
| Direção/patrocinadores (Vinícius e Fernanda) | Visão de exposição, andamento, compromissos, riscos e decisões de prioridade. | Definir políticas, validar resultados e decidir exceções; não se presume acesso irrestrito a dados financeiros pessoais ou segredos comerciais sem política formal. |
| Champion/analista de dados (Fábio) | Integrar e reconciliar fontes, manter modelo de dados, administrar aplicação e investigar divergências. | Responsável técnico indicado; acesso e autoridade precisam ser provisionados pela Fortimber. |
| Produção/indústria | Ver plano, necessidade, ordens/lotes, produto acabado e divergências de quantidade. | Atualizar ou validar eventos de produção conforme processo definido; sistema de origem e granularidade ainda precisam ser mapeados. |
| Comercial/contratos | Registrar/consultar consultas, cotações quando incluídas, contratos/pedidos, prazos e compromissos; acompanhar saldo. | Validar dados comerciais e administrar alterações contratuais autorizadas. |
| Financeiro | Relacionar faturamento/recebimentos e resultado quando essa cobertura estiver validada. | Confirmar definições e acesso a dados do Omie; não inferir cálculo contábil de margem. |
| TI/fornecedores de sistemas | Liberar acesso, APIs, cópias de leitura, documentação e suporte. | Autorizar tecnicamente as integrações; não se assume que acesso esteja disponível. |

Papéis, grupos, segregação por filial e matriz de autorização serão confirmados antes do uso com dados reais. As funções acima são grupos de trabalho, não um desenho de IAM já aprovado.

## 4. Escopo funcional

### Incluído no arco do projeto

1. **Cadastro/visão de contratos e pedidos:** identificar cliente, mercado, espécie, produto/especificação, quantidade/unidade, preço/moeda, datas relevantes, situação e fonte. O conjunto exato de campos será confirmado por amostra de contratos nacionais e internacionais.
2. **Saldo contratual:** mostrar quantidade acordada, alterações aprovadas, quantidade entregue/faturada quando disponível e saldo calculado, com fórmula e unidade visíveis. Não somar unidades incompatíveis.
3. **Vínculo demanda–produção–estoque:** associar necessidade e produção a contrato/pedido ou a estoque livre, distinguindo vínculo confirmado, estimado e não alocado.
4. **Visão de produção e disponibilidade:** expor quantidades disponíveis, comprometidas, em produção e previstas, com data de referência, fonte e confiabilidade. Previsões não serão apresentadas como estoque confirmado.
5. **Entregas e expedição:** registrar/importar eventos de expedição/embarque/entrega e relacioná-los ao contrato, quando a fonte suportar. A semântica de “entregue” (saída da indústria, embarque, chegada ou aceite do cliente) deverá ser definida por mercado/processo.
6. **Painel de acompanhamento:** filtros por período, cliente, contrato/pedido, produto/espécie, filial/entidade e status, condicionados à qualidade dos dados; exibir saldos, progresso e exceções.
7. **Alertas operacionais explicáveis:** destacar saldo sem produção vinculada, divergência de quantidade, prazo próximo/ultrapassado, estoque comprometido em excesso, fonte desatualizada ou falta de informação. Fase 1–3 prioriza alertas determinísticos; recomendação por IA só entra com validação e evidência.
8. **Rastreabilidade e auditoria:** para cada indicador/registro, apontar fonte, identificador de origem, instante de atualização e transformações essenciais; registrar alterações manuais, autor, data e justificativa, se houver escrita no sistema.
9. **Integração progressiva:** começar pelos sistemas com acesso estável e valor comprovado; suportar importação controlada por arquivo/relatório como contingência documentada quando API não existir.
10. **Camada conversacional/loops nas fases 4–5:** explorar perguntas em linguagem natural e rotinas supervisionadas, limitadas a consultas autorizadas e ações explicitamente aprovadas.

### Não incluído, salvo aprovação de mudança

- Substituir ou reimplementar integralmente Sysflora/SISFLORA, Dolphin, SISAP, Omie, Siscomex ou o sistema do porto.
- Escrita automática nesses sistemas, negociação/preço automático, aceite de contrato, reserva irreversível, ordem de produção sem aprovação humana ou lançamento financeiro sem autorização.
- Raspagem do portal governamental em produção, quebra de CAPTCHA/controle de acesso ou armazenamento de senha pessoal. Qualquer integração de portal depende de autorização, termos aplicáveis, mecanismo seguro e análise de estabilidade; senha rotativa exige fluxo explícito de atualização.
- ERP completo, contabilidade, folha, compras, manutenção, CRM amplo, gestão de clientes/prospecção, cobrança e pós-venda genéricos.
- Envio de e-mail/WhatsApp ao cliente sem conteúdo, destinatários, consentimentos/autorizações, gatilho e aprovador definidos. Nenhum agente poderá enviar ou publicar por conta própria no escopo inicial.
- Otimização matemática de corte, rendimento florestal, roteirização de transporte, previsão de preços ou recomendação financeira sem dados, regras e validação especializada.
- Promessa de comercialização do sistema como SaaS/multicliente. A fala de que o sistema poderia futuramente ser comercializado é intenção exploratória, não requisito confirmado; arquitetura multiempresa e obrigações de produto ficam fora desta versão.
- Eliminar planilhas/sistemas de origem antes de um período de reconciliação e aceite formal.

## 5. Fluxos de negócio

### Fluxo atual resumido

1. Plano de manejo e inventário determinam a madeira prevista; a meta indicada no DMO é explorar aproximadamente 2.500 ha/ano.
2. Operações florestais registram atividades em sistema de terceiro: extração, movimentação, carregamento/transporte e chegada à indústria.
3. A indústria registra transformação de toras em madeira serrada/beneficiada e etapas até embarque, também em sistema de terceiro.
4. No mercado nacional, o cliente contata por telefone/WhatsApp, a equipe verifica espécie e disponibilidade, cota, fecha e encaminha para produção/entrega.
5. No mercado internacional, trade encaminha consulta por e-mail; Fortimber informa preço/prazo quando há disponibilidade; contrato é recebido quando a venda fecha.
6. Informações de contrato são controladas majoritariamente em Excel, com consumo em Power BI e consultas a diferentes sistemas e pessoas.
7. Fábio iniciou uma área de dados e integração; Omie já foi integrado segundo a reunião técnica. Siscomex e porto possuem API, ainda por estudar. Não há API conhecida para Sysflora. Acesso a cópia de leitura do banco SISAP estava em negociação.

### Fluxo futuro desejado

1. Importar ou registrar contrato/pedido com identificadores e unidades normalizadas.
2. Validar campos obrigatórios e origem; sinalizar incompletude sem inventar valores.
3. Consolidar produção, estoque e entregas das fontes disponíveis, preservando data de atualização e referência de origem.
4. Relacionar produção/estoque a contrato/pedido por vínculo informado ou regra aprovada; manter produto sem vínculo em “não alocado” até decisão responsável.
5. Calcular saldo e disponibilidade com regras versionadas e unidades compatíveis.
6. Apresentar visão de andamento e exceções; responsável avalia risco e registra decisão/justificativa quando cabível.
7. Atualizar a visão após novos eventos de produção, faturamento ou expedição e preservar histórico de atualização.

**Atenção:** os passos 1–7 descrevem capacidade pretendida, não o processo operacional já validado. As exceções de conversão, aproveitamento, perdas, substituição de produto, tolerâncias de contrato, cancelamentos e aditivos exigem descoberta com casos reais.

## 6. Dados e regras de domínio

### Entidades conceituais mínimas

- Cliente/contraparte e trade/intermediário (quando aplicável).
- Contrato e pedido, com aditivos/versões sem sobrescrever o histórico.
- Produto e especificação comercial; espécie; unidade de medida e conversões aprovadas.
- Origem/manejo/lote florestal, quando disponível e permitido pela fonte.
- Lote/ordem/evento de produção, etapa, quantidade, unidade e data.
- Estoque por local/produto/lote, distinguindo disponível, reservado, bloqueado e em trânsito se as fontes permitirem.
- Reserva/alocação para contrato ou pedido, incluindo quantidade, responsável, data e estado.
- Expedição/embarque/entrega e documentos associados.
- Faturamento e valores financeiros apenas conforme fonte e definição aprovada.
- Registro de integração: sistema, chave de origem, instante da extração, resultado, erro e versão de transformação.

### Regras iniciais de apresentação e cálculo

1. **Saldo de contrato:** saldo = volume vigente contratado − volume reconhecido como entregue conforme evento e regra aprovados. Alterações contratuais devem preservar histórico e recalcular somente a partir da versão vigente.
2. **Disponibilidade:** deve separar saldo físico confirmado, quantidade em produção/previsão e quantidade já comprometida. Não somar previsão à disponibilidade física sem rotulagem.
3. **Rastreabilidade:** vínculo a contrato/pedido é evidência explícita; item sem vínculo permanece identificável como não rastreado. A meta inicial de 95% refere-se à produção/estoque, mas denominador, período, unidade e tratamento de itens sem contrato devem ser fechados no baseline.
4. **Tempo de atualização:** toda visão mostra “atualizado em” por fonte. Fonte atrasada não é exibida como estado atual sem aviso.
5. **Divergências:** preservar os valores originais e expor discrepância; não escolher silenciosamente qual sistema está correto.
6. **Unidades:** conversão (por exemplo, volume, peças, m³, peso, embalagem/contêiner) só ocorre por tabela aprovada, com precisão e arredondamento definidos.
7. **Preço/resultado:** exibir moeda e condição do registro. Margem/resultado projetado versus realizado fica dependente de definição de custos, câmbio, impostos, frete e momento de reconhecimento; não inferir margem contábil.
8. **Rastreabilidade de origem:** cadeia de custódia depende de identificadores e evidências existentes nas fontes. A aplicação não certifica legalidade por si só; apresenta trilha e lacunas para validação humana.

## 7. Arquitetura funcional e integrações

### Direção arquitetural

Construir uma aplicação própria que consuma uma camada de dados consolidada, aproveitando a área de dados existente e evoluindo-a conforme validação. A reunião técnica citou Postgres, Python e VM atuais; Supabase, Skip, GitHub, React/TypeScript, IDE e MCP foram apresentados como caminhos de implementação possíveis. A escolha final de banco gerenciado, hospedagem, framework, repositórios e ambientes (desenvolvimento/homologação/produção) depende de revisão técnica de Fábio/TI e não é fixada por este escopo.

Princípios obrigatórios de implementação:

- integração incremental, idempotente e monitorável;
- leitura como padrão nas fontes externas; escrita só após decisão explícita e análise de risco;
- separação de segredos por ambiente, sem credenciais em código, prompt, planilhas ou repositório;
- privilégio mínimo, autenticação individual e autorização por função/dado conforme política Fortimber;
- trilha de auditoria e proveniência para registros consolidados;
- dados pessoais e comerciais tratados sob política de acesso, retenção e LGPD definida pela Fortimber;
- ambientes e cópias de dados de teste anonimizados ou minimizados quando possível;
- falhas de integração visíveis, repetição segura e reconciliação antes de publicar indicadores.

### Mapa inicial de fontes

| Fonte | Situação conforme reunião | Papel no escopo | Dependência/risco |
|---|---|---|---|
| Excel e Power BI | Controle atual principal/paralelo; consolida informações de múltiplos sistemas. | Fonte transitória e referência de reconciliação para a primeira visão. | Identificar dono, arquivos, fórmula, atualização, duplicatas e limites de qualidade. |
| Área de dados / Postgres em VM | Fábio já iniciou; extração de Omie e contratos reportadas. | Camada candidata para integração e modelo analítico. | Confirmar arquitetura, operação, backup, acessos, monitoramento e separação de ambientes. |
| Omie | API citada como acessível e já integrada à área de dados. | Financeiro/faturamento e dimensões relacionadas, conforme endpoints autorizados. | Inventariar entidades, limites, chaves, periodicidade e correspondência com contratos. |
| Siscomex | API disponível, ainda não estudada por Fábio. | Dados de exportação/documentação que forem relevantes ao ciclo contratual. | Avaliar documentação, credenciais, escopo, limites e entidades exatas. |
| Sistema do porto | API citada como existente, ainda não estudada. | Marcos de embarque/porto quando pertinentes. | Nome do sistema, dono, API, semântica dos eventos e acesso pendentes. |
| Sysflora/SISFLORA | Sem API aberta conhecida; relatórios exportáveis foram sugeridos; portal pode exigir login e senha rotativa. | Dados florestais, origem, movimentação e cadeia de custódia, prioritariamente por relatório autorizado no início. | Não assumir scraping. Confirmar nome oficial, relatórios, periodicidade, autorização e possibilidade de integração estável. |
| Dolphin | Sistema de transformação industrial citado nas atas; mecanismo de integração não confirmado. | Produção industrial, transformação e produto acabado. | Confirmar se é módulo do fornecedor de produção, API, banco de leitura ou exportação. |
| SISAP | Acesso de leitura a cópia do banco de produção em negociação com fornecedor/programador. | Possível fonte de dados operacionais ou contratos. | Não há acesso aprovado no material; depende de autorização, contrato, esquema e segurança do fornecedor. |
| E-mail (Gmail mencionado) | Canal central de comunicação comercial/exportação. | Fase futura pode indexar referências ou suportar notificações aprovadas. | API OAuth/permissões, caixas compartilhadas, consentimento, classificação e política de retenção. Não ingerir indiscriminadamente. |

## 8. Plano de cinco fases

As fases são incrementos de sistema, não apenas tarefas preparatórias. Fases 4 e 5 também acrescentam loops/agentes candidatos. A fase 5 inclui validação ponta a ponta de todas as entregas anteriores. A sequência abaixo é a proposta deste escopo; cronograma detalhado, esforço e ordem de integração serão ajustados após descoberta técnica. O período de quatro meses é o horizonte de acompanhamento citado no kick-off, não uma garantia de que todas as integrações externas ou decisões de terceiros estarão concluídas nesse prazo.

### Fase 1 — Painel confiável de contratos ativos e saldos

**Resultado demonstrável:** usuários autorizados consultam numa aplicação única contratos ativos, volume vigente, produzido/vinculado quando disponível, entregue, saldo, prazo e fonte de cada campo. A tela identifica informação ausente ou desatualizada. A primeira entrega dá valor mesmo que parte da atualização ainda use importação controlada.

**Capacidades do sistema**

- Importar/normalizar conjunto inicial de contratos e pedidos, priorizando os contratos ativos selecionados com Fortimber.
- Exibir lista e detalhe de contrato com cliente, produto/espécie, quantidade/unidade, preço/moeda, datas, estado, produção associada, entregas reconhecidas e saldo.
- Filtrar por cliente, período, mercado, produto e estado, apenas para campos disponíveis/normalizados.
- Exibir fonte, data de atualização, identificador de origem e estado de qualidade.
- Sinalizar contrato sem quantidade/unidade, sem vínculo de produto, saldo não calculável ou divergência de fonte.
- Permitir reconciliação inicial via importação CSV/XLSX versionada ou integração de leitura já disponível, sem substituir planilhas como fonte oficial antes do aceite.

**Atores:** Comercial/contratos consulta e confere; Champion administra carga e mapeamento; patrocinadores revisam amostra e aprovam definição do saldo.

**Dados e fontes:** contratos ativos e entregas/produção selecionadas; Excel/Power BI como referência de reconciliação; Postgres/área de dados candidata. Integração não presumida além do que estiver tecnicamente pronto.

**Regras/dependências:** dicionário de campos; chave única de contrato/pedido; relação contrato–aditivo; unidades e definição de entregue; amostra representativa nacional/internacional. Nenhuma alocação automática ou escrita em sistema operacional.

**Fora desta fase:** previsão futura robusta, substituição de fonte operacional, margem líquida, notificações a clientes, agente IA autônomo.

**Aceite binário:** (a) cada contrato da amostra aprovada aparece uma única vez ou duplicidade é sinalizada; (b) saldo da amostra confere com regra e evidência homologadas; (c) campos ausentes/desatualizados são marcados, nunca preenchidos silenciosamente; (d) usuário de cada perfil aprovado só vê funções/dados liberados; (e) evidência de fonte/atualização pode ser consultada.

### Fase 2 — Integrações e reconciliação das fontes prioritárias

**Resultado demonstrável:** dados prioritários entram de forma repetível da(s) fonte(s) com acesso autorizado; falhas, atrasos e divergências ficam visíveis e podem ser reconciliados. A cobertura é explícita por fonte, sem afirmar integração completa da empresa.

**Capacidades do sistema**

- Conector de leitura para a primeira fonte priorizada após inventário técnico (candidata inicial: Omie, já integrada à área de dados, ou outra fonte que reduza o maior risco de contrato/saldo).
- Inventário de API e carga de Siscomex/porto/Dolphin/SISAP por prioridade acordada, habilitando somente fontes com acesso autorizado e mapeamento validado.
- Importador controlado de relatório/arquivo de Sysflora quando necessário e autorizado, com validação de cabeçalho, período e duplicidade.
- Registro de execução por fonte: início/fim, registros lidos, aceitos, rejeitados, erro e último sucesso.
- Chaves de correspondência e regras de deduplicação documentadas; fila/lista de divergências para revisão humana.
- Reprocessamento idempotente e histórico de carga, sem duplicar contratos/eventos.

**Atores:** Champion/administrador acompanha conectores; donos funcionais validam reconciliação; TI/fornecedor autoriza acesso.

**Dados e integrações:** implementar apenas fontes e entidades selecionadas na matriz de prioridade; Postgres ou camada de dados aprovada. Credenciais por mecanismo seguro e privilégio de leitura.

**Regras/dependências:** termo de acesso, documentação/credenciais, chaves comuns, frequência necessária, definição de “fonte da verdade” por campo e política de erro/reprocessamento. A frequência não será presumida; deve ser acordada com o negócio e o limite técnico.

**Fora desta fase:** scraping frágil sem autorização, integração com toda API apenas por existir, sincronização bidirecional ou descarte de Excel antes de reconciliação aprovada.

**Aceite binário:** execução pode ser repetida sem duplicar eventos; estado e horário por fonte aparecem; falha gera sinal visível; amostra por fonte reconcilia com origem; fonte ainda não integrada permanece identificada como lacuna e não contamina métricas como se estivesse completa.

### Fase 3 — Produção, estoque, disponibilidade e planejamento

**Resultado demonstrável:** a equipe visualiza disponibilidade presente e futura separando estoque confirmado, produção em curso/previsão e volume comprometido, e consegue entender que contrato ou necessidade está associado a cada quantidade.

**Capacidades do sistema**

- Consolidar eventos de madeira/origem, entrada industrial, transformação, produto acabado, estoque, reserva e expedição conforme disponibilidade das fontes.
- Relacionar espécie/produto e suas especificações entre contrato e produção por tabela de equivalências aprovada.
- Exibir disponibilidade por produto/espécie/local/período quando a granularidade existir, distinguindo confirmado, estimado, comprometido, bloqueado e sem classificação.
- Apresentar necessidade de produção associada aos saldos de contratos e pedidos; permitir ver lacunas entre demanda comprometida e oferta/produção prevista.
- Mostrar riscos determinísticos: prazo sem quantidade prevista suficiente, compromisso superior ao estoque/produção elegível, lote não rastreado ou dado fora da janela de atualização aprovada.
- Apresentar cenário informativo de priorização; a decisão de priorizar/alterar plano fica com responsável humano e registra justificativa.

**Atores:** Produção/indústria valida eventos, rendimentos e situação física; Comercial confirma compromissos; liderança decide prioridades; Champion mantém mapeamentos.

**Dados e fontes:** saída das fases 1–2 e fonte operacional validada (Dolphin/Sysflora/SISAP ou arquivo aprovado); dimensões de espécie/produto/unidade/local; contrato e aditivos.

**Regras/dependências:** rendimento/perda e conversão; definição de estoque físico e disponível; transferência entre locais; reserva, cancelamento e substituição; calendário e capacidade produtiva; horizonte e critério de previsão. Só os itens confirmados entram em cálculo automatizado.

**Fora desta fase:** otimização automática do plano de corte/produção, execução automática de ordem ou promessa comercial, previsão de mercado e certificação automática de cadeia de custódia.

**Aceite binário:** para casos homologados, totais de estoque/produção/compromisso reconciliam com as fontes segundo tolerâncias aprovadas; classes confirmado/estimado/comprometido são distintas; cada vínculo rastreável aponta à fonte/lote e contrato ou aparece como exceção; cenários não gravam ordens sem aprovação.

### Fase 4 — Sistema de exceções, acompanhamento e loops supervisionados

**Incremento de sistema:** fila operacional de exceções e histórico de decisões, conectada às visões de contratos, produção e disponibilidade. Responsáveis podem classificar, atribuir, registrar decisão e encerrar exceção, mantendo trilha de auditoria.

**Resultado demonstrável:** cada exceção de contrato/produção visível tem motivo, dados que a sustentam, responsável/estado e resolução registrada; liderança acompanha pendências em vez de reconstruir o caso por e-mail.

**Capacidades do sistema**

- Caixa de trabalho por contrato e tipo de risco, com filtro por responsável, prazo e criticidade configurada.
- Histórico de status/decisão/justificativa e referência à evidência de origem.
- Resumo de andamento por contrato e preparação de atualização interna para revisão humana.
- Controles de notificação interna somente se canal e destinatários forem aprovados; envio externo permanece desligado até aprovação específica.

**Loops/agentes candidatos (camada adicional, não substitui o incremento do sistema)**

1. **Loop de monitoramento de contratos em risco:** meta candidata — revisar contratos ativos em cada ciclo e produzir uma lista de possíveis atrasos, falta de volume ou dados desatualizados, com evidência e responsável sugerido. Métrica candidata: proporção de alertas confirmados como úteis e antecedência em relação ao prazo. Baseline, alvo, cadência e prazo serão definidos após medir a qualidade das fases 1–3. Saída apenas como recomendação; humano confirma/descarta.
2. **Agente de consulta operacional:** responder perguntas em linguagem natural usando somente visões autorizadas (por exemplo, saldo e progresso de contrato), citar atualização e fonte, pedir esclarecimento se houver ambiguidade e recusar resposta quando os dados forem insuficientes. Sem escrita ou envio externo.

**Atores:** responsável funcional trata exceções; liderança avalia prioridades; champion aprova consultas, fontes e logs; destinatário do alerta valida a utilidade.

**Dados/integrações:** visões consolidadas, histórico de exceção e fontes ativas; conector de agente ao banco/consulta read-only, quando aprovado. Não entregar segredos ou conteúdo integral de e-mail ao modelo por padrão.

**Aceite binário:** exceção aponta evidências consultáveis; decisão registra ator/data; agente cita fonte/data de atualização e não inventa quando dado falta; loop apresenta somente recomendações até autorização explícita; medição de baseline e revisão humana existem antes de atribuir ganho.

### Fase 5 — Cobertura final do sistema, loops aprovados e validação ponta a ponta

**Incremento de sistema:** completar as capacidades priorizadas ainda pendentes do painel operacional e da cobertura integrada, dentro das fontes e decisões aprovadas, incluindo fechamento de lacunas de rastreabilidade priorizadas. Não é fase somente de teste.

**Loops/agentes adicionais:** amadurecer o loop de risco ou agente de consulta conforme evidência e aprovação; avaliar uma rotina de atualização a clientes por e-mail como candidata separada. A rotina externa só poderá ser ativada após aprovar conteúdo, gatilhos, destinatários, frequência, canal, consentimento/autoridade, tratamento de falha e reversão. WhatsApp não é incluído sem escolha de provedor e validação de custos/política.

**Resultado demonstrável:** o fluxo contrato → programação/produção → estoque/disponibilidade → alocação → expedição/entrega → saldo pode ser percorrido com dados de origem e estados conhecidos, e cada indicador global tem evidência e limitações documentadas.

**Validação transversal obrigatória das fases 1–5**

- Regressão funcional dos critérios aceitos nas fases 1, 2, 3, 4 e 5.
- Teste integrado de fontes, contrato, produção, estoque, exceções, agentes/loops e notificações aprovadas.
- Conferência de autorização por perfil, segregação de dados, auditoria, minimização/retensão e tratamento LGPD conforme política definida pela Fortimber.
- Falhas e recuperação: fonte indisponível, atraso, dado duplicado, aditivo, evento fora de ordem, unidade incompatível, timeout, reprocessamento e rollback.
- Reconciliação de amostras ponta a ponta, incluindo casos nacionais/internacionais e registros sem vínculo.
- Avaliação de loop com baseline, alvo aprovado, cadência, fonte de medição, falsos positivos/negativos e responsável humano pelo veredito.
- Champion conduz roteiro com usuários; aceite humano registra evidências, pendências e risco residual. Item não testado continua pendente.

**Critérios globais de aceite propostos**

1. **Cobertura de contratos ativos:** ≥95% dos contratos ativos com saldo têm volume contratado, produzido, entregue e disponibilidade futura consolidados ou explicitamente marcados como indisponíveis, com fonte e data. Atingimento da métrica exige que a Fortimber aprove denominador, definição de contrato ativo, janela, unidade e tratamento de ausência de fonte.
2. **Rastreabilidade:** ≥95% da produção/estoque no universo medido tem trilha até contrato/pedido correspondente ou classificação explícita de estoque ainda não comprometido, mantendo a cadeia de origem disponível quando fornecida pela fonte. Denominador, nível de lote, período e exclusões precisam ser aprovados.
3. **Riscos antecipados:** 100% dos riscos que possam ser detectados pelas regras/dados habilitados são sinalizados antes do prazo de ação configurado. Não significa detecção de todo risco comercial nem garantia de antecedência sem fonte/frequência suficiente.
4. **Confiança e reconciliação:** totais da amostra homologada batem com sistemas de origem dentro de tolerâncias definidas por entidade/unidade; divergências restantes são visíveis e têm responsável ou justificativa.
5. **Operação segura:** nenhum conector ou agente executa escrita/envio externo sem permissão específica aprovada; falhas não produzem duplicidade silenciosa; cada indicador apresenta atualização e proveniência.

## 9. Segurança, privacidade e governança

- A Fortimber define controlador, finalidades, base legal, classificação, retenção, solicitações dos titulares e responsáveis por dados pessoais. Dados de funcionários/clientes e conteúdo de e-mail devem ser minimizados.
- Acesso individual e privilégio mínimo; contas compartilhadas e credenciais expostas em prompts, env ou repositórios são proibidas. Rotação e armazenamento seguro de segredo são responsabilidades técnicas a detalhar com TI.
- API keys/OAuth e cópia de banco devem usar escopo read-only sempre que possível. Acesso de fornecedor ao banco SISAP ou a qualquer produção requer aprovação formal e rede/ambiente controlados.
- Agentes consultam views aprovadas/read-only, com autorização equivalente à do usuário; prompts não substituem controles do banco. Logs não devem registrar tokens, senhas ou conteúdo pessoal desnecessário.
- Isolar ambientes de desenvolvimento/homologação/produção e reduzir ou anonimizar amostras de dados reais usadas em teste.
- Política de backup, recuperação, auditoria, prazo de retenção e resposta a incidente precisa ser acordada com o time de TI antes do go-live.
- Requisitos de LGPD, contratos de operador/controlador e avaliação de provedores externos são verificados pela Fortimber. Falas sobre certificações de fornecedor de IA não substituem avaliação jurídica/técnica da solução Fortimber.
- Segurança da cadeia de custódia: a aplicação mantém ligações e documentos disponíveis; validação legal/regulatória e certificação permanecem com responsáveis competentes.

## 10. Indicadores e medição

| Indicador | Definição a operacionalizar | Fonte candidata | Situação |
|---|---|---|---|
| Contratos ativos com saldo | Contagem e proporção com quantidade, produzido, entregue, saldo e disponibilidade futura consolidados. | Cadastro de contratos + produção/estoque/entrega; Excel/Power BI como reconciliação inicial. | Critério inicial acordado; definição de ativo, período e cobertura pendentes. |
| Volume contratado, produzido e entregue | Totais por produto/espécie/unidade e período, sem misturar unidade não convertida. | Sistemas de contrato/produção/expedição. | Métrica acordada em conceito; unidades, evento de reconhecimento e filtros pendentes. |
| Disponibilidade futura | Volume esperado menos compromissos, segregando estoque confirmado e projeções. | Produção/estoque e contratos. | Conceito acordado; horizonte, regras e confiança da previsão pendentes. |
| Rastreabilidade produção/estoque | Parcela do universo medido com vínculo válido a pedido/contrato e referência à origem disponível. | Fonte de produção, lote/estoque e contrato. | Meta inicial de 95% aceita como direção; denominador e protocolo de auditoria pendentes. |
| Alertas antecipados | Riscos detectáveis sinalizados até a antecedência mínima definida e avaliados pelo responsável. | Regras do sistema + registro de exceções. | Métrica proposta; riscos cobertos, antecedência e falso positivo pendentes. |
| Resultado/margem por contrato | Projetado versus realizado segundo política de custo, câmbio, imposto, frete e reconhecimento. | Financeiro/Omie e custos operacionais. | Indicador exploratório no briefing; não entra como KPI de aceite até definição financeira aprovada. |

**Baseline:** levantar por um período acordado os números atuais, completude das fontes, tempo para responder status de contrato, volume de divergências e trabalho manual. Sem baseline, não declarar redução percentual de custo ou aumento de margem como resultado comprovado.

## 11. Riscos, premissas e mitigação

| Risco/premissa | Impacto | Resposta prevista |
|---|---|---|
| Fonte operacional sem API, instável ou restrita por fornecedor | Cobertura incompleta ou atraso; divergência em disponibilidade. | Começar por fontes acessíveis; usar arquivo autorizado com carimbo de data como contingência; documentar lacuna; não prometer scraping. |
| Falta de mapeamento real dos processos | Erro de regra para saldo, produção, perdas, alocação e entrega. | Validar casos reais e exceções com Fernanda, Fábio, Vinícius e operadores antes das SPECs de cada incremento. |
| Identificadores diferentes entre contrato, produto, lote e sistema | Vínculos falsos ou dados duplicados. | Dicionário de correspondência, regra explícita, fila de exceção e aceite de amostras. |
| Definições de unidade e produto incompatíveis | Totais incorretos e decisão operacional errada. | Catálogo e conversões aprovados por negócio; impedir conversão silenciosa. |
| Área de dados depende de uma pessoa | Operação e manutenção frágeis. | Documentação, versionamento, rotinas de recuperação e capacitação de ao menos um substituto a definir. |
| Credenciais rotativas de portal e scraping | Interrupção e risco de segurança/termos de uso. | Evitar scraping na primeira entrega; avaliar mecanismo autorizado e seguro apenas após análise. |
| IA responde com dado incompleto/incorreto | Decisão errada, exposição comercial. | Agente read-only com views controladas, fonte/data em respostas, tratamento de desconhecido, validação humana e telemetria. |
| Projeto tenta abranger sistema próprio de produção inteiro | Escopo e prazo explodem; duplicação de sistemas. | Integrar primeiro; substituir sistemas é decisão futura baseada em business case próprio. |
| Desejo de SaaS/multicliente futuro | Requisitos de isolamento, segurança, suporte e produto alteram arquitetura. | Manter fora desta versão; abrir decisão de produto antes de projetar multi-tenancy. |
| Uso de dados reais em ferramentas externas/IA | Exposição de dados e violação de política. | Aprovar provedor, região, finalidade e fluxo de dados; mascarar/minimizar; não conectar sem autorização. |

## 12. Decisões pendentes para aprofundamento e aceite

As decisões abaixo não impedem este escopo de orientar a próxima etapa, mas precisam estar fechadas nas SPECs antes de implementar o comportamento correspondente.

1. **Processos e exceções:** fornecer walkthrough/vídeos ou sessões de processo para os três fluxos do roteiro PDF; validar fluxo real e responsáveis.
2. **Universo inicial:** selecionar contratos ativos reais (nacionais e internacionais), número de empresas/filiais e período piloto; confirmar os dados representativos.
3. **Definições:** contrato ativo, saldo, volume produzido, volume entregue, disponibilidade futura, estoque elegível e rastreabilidade válida; fórmula, unidade, evento, tolerância e denominador.
4. **Fontes de verdade:** dono de cada campo e preferência em divergência; confirmar produto oficial e papel de Dolphin, SISAP e Sysflora/SISFLORA.
5. **Integrações:** nome/documentação do sistema portuário, APIs Siscomex, endpoints Omie já consumidos, relatórios exportáveis de Sysflora, acesso/cópia read-only do SISAP e permissão do fornecedor.
6. **Infraestrutura:** decisão entre manter VM/Postgres existente ou migrar/usar Supabase; hospedagem, ambientes, backup, monitoramento, RPO/RTO, custo e responsável operacional.
7. **Identidade e acesso:** autenticação, perfis, acesso por filial, usuários externos, política de senha/MFA e auditoria.
8. **Notificações externas:** canal (e-mail/WhatsApp), gatilhos, templates, idioma, destinatários, janela, aprovação humana, política de opt-out e custo/provedor.
9. **Agente/loops:** confirmar se entram após sistema determinístico validado; baseline, alvo, cadência, fontes, dono do veredito e limites de autonomia.
10. **Financeiro/margem:** definir margem/resultado por contrato e componentes, período, câmbio, custos e fonte oficial antes de expor como número decisório.
11. **Comercialização externa:** confirmar se continua apenas hipótese; se aprovada, abrir discovery separado de produto SaaS, multiempresa, suporte, contratos, LGPD e segurança.
12. **Aceite e operação:** nomear aprovadores funcionais por área, ambiente de homologação, roteiro de UAT, canal de incidentes e condição de go-live.

## 13. Matriz resumida de rastreabilidade

| Fonte/achado | Requisito/decisão no escopo | Fase |
|---|---|---|
| Kick-off: liderança precisa consultar várias pessoas/sistemas para saber status de contrato; controle majoritário em Excel/Power BI. | Painel de contrato, saldo, progresso e proveniência; reconciliação inicial. | 1 |
| Kick-off: objetivo acordado de integrar planejamento de produção e contratos; métricas de contratado/produzido/entregue/disponibilidade; meta inicial de 95% de rastreabilidade. | KPIs globais, saldo e trilha de produção/estoque com denominador e regra a validar. | 1, 3, 5 |
| DMO: cadeia verticalizada, meta de 2.500 ha/ano, consumir/produzir no ciclo; margem via eficiência e rastreabilidade. | Visão de origem–produção–estoque–compromisso; preservar a decisão humana; cadeia de custódia com evidência de origem. | 3, 5 |
| Reunião técnica: Omie API; Siscomex/porto APIs por estudar; Sysflora sem API; SISAP read-only pendente; cerca de 4.500 linhas de contratos. | Integração incremental, importação/reconciliação, monitoramento e limite explícito de cobertura. | 1, 2, 3 |
| Reunião técnica: possibilidade de e-mail/WhatsApp e agente; ainda sem parâmetros aprovados. | Apenas candidato supervisionado; comunicação externa e autonomia bloqueadas até decisão. | 4, 5 |
| Roteiro PDF: mapear contrato→programação; produção→estoque; acompanhamento de contrato. | Fluxos, regras e exceções como entrada das SPECs; riscos registrados como pendência. | 1–3 |
| Desejo mencionado de comercializar e substituir terceiros. | Fora do escopo corrente; decisão futura separada de SaaS/substituição integral. | Fora do escopo |

## 14. Critério de encerramento do projeto

O projeto pode ser considerado entregue quando: (1) capacidades priorizadas das cinco fases estiverem implementadas e demonstradas; (2) indicadores globais forem medidos contra fontes e definições aprovadas; (3) amostras ponta a ponta de contrato, produção, estoque e entrega estiverem reconciliadas; (4) lacunas conhecidas e fontes não integradas estiverem expostas; (5) usuários e champion concluírem o roteiro de validação e aceitarem ou registrarem formalmente pendências; (6) credenciais, monitoramento, recuperação, backup, documentação e responsáveis de operação estiverem definidos; e (7) decisão de go-live/continuidade for registrada pela Fortimber. Atingir a meta de 95% não elimina a obrigação de evidenciar o restante não rastreado.

## 15. Fontes do workspace

- `03-Projeto/00-DMO.md` — respostas do Diagnóstico de Maturidade, 10/08/2026.
- `02-Reuniao/Kickoff Call/01-transcricao.md`, `02-Ata_reuniao.md`, `03-fluxos_encontrados.md`, `04-decisoes_pendencias.md`, `05-insights_ASA.md` — kick-off de 17/08/2026.
- `02-Reuniao/Sales Call/01-transcricao.md`, `02-Ata_reuniao.md`, `03-fluxos_encontrados.md`, `04-decisoes_pendencias.md`, `05-insights_ASA.md` — conjunto documental existente com transcrição de kick-off duplicada; não adiciona evidência independente à ata de 17/08.
- Transcrição colada pelo usuário (“Kimberly Prestes [00:00]...”), correspondente à reunião técnica de 16/09/2026 conforme `02-Reuniao/00-Indice_reunioes.md`.
- `04-Mapeamento-Processos/00-Contexto/[Fortimber] Tarefas para o Mapeamento de Processos.pdf` — roteiro proposto para descoberta.
- `04-Mapeamento-Processos/00-Contexto/fortimber.pdf` — briefing e síntese diagnóstica.
- `04-Mapeamento-Processos/02-Processos_mapeados/01-Controle de Produção.md` — arquivo sem fluxo preenchido na data da análise.
