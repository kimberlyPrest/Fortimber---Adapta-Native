# SPEC-F1-03 — Painel e detalhe de contratos

**Fase:** 1
**Estado:** definição funcional; UI implementa apenas os campos e regras validados em SPEC-F1-01.
**Dependências:** amostra publicada da SPEC-F1-02 e regras aprovadas em SPEC-F1-01.

## 1. Objetivo e resultado

Dar a Comercial, Produção e liderança uma página de consulta única para localizar contratos ativos e entender rapidamente quantidade vigente, produção associada quando disponível, entrega reconhecida, saldo, prazo e qualidade/atualidade do dado. A interface deve ajudar a descobrir o que falta saber, e não esconder lacunas para aparentar completude.

## 2. Atores e acesso

- **Leitor comercial/produção/liderança:** consulta a lista e detalhe conforme autorização aprovada.
- **Champion/administrador:** consulta detalhes de qualidade, lote e proveniência; administra carga apenas se o perfil receber tal permissão.
- **Usuário não autorizado:** não acessa dados protegidos e recebe resposta segura sem confirmar se um contrato específico existe.
- O mapeamento de perfil para permissão é aprovado na SPEC-F1-04; não inferir acesso por função sem aprovação Fortimber.

## 3. Conteúdo da lista

Para cada linha de contrato/item exibida, incluir quando existir e estiver mapeado:

- cliente/contraparte e trade/intermediário;
- número de contrato e pedido, origem e empresa/filial quando aplicável;
- mercado (nacional/internacional);
- produto, espécie e especificação;
- situação e datas relevantes (vigência/prazo);
- quantidade contratada vigente e unidade;
- volume produzido associado, separado do volume entregue;
- volume entregue reconhecido e saldo calculado segundo SPEC-F1-01;
- aviso de incompletude, conflito, estimativa ou dado desatualizado;
- timestamp de referência dos dados, com clareza se fontes tiveram horários diferentes.

Campos sem valor não devem desaparecer de modo a induzir interpretação; apresentar estado textual padronizado (“não informado”, “não integrado”, “em divergência”, “não calculável” ou equivalente validado) e indicar a origem do problema.

### Filtros e ordenação

- Filtros apenas sobre campos canônicos: período/prazo, cliente, mercado, produto/espécie e situação.
- Critério inicial de lista: contratos ativos; usuário autorizado pode incluir encerrados se a política e a SPEC-F1-01 permitirem.
- Ordenação por prazo mais próximo e cliente/contrato; ordenação deve ser estável para resultados iguais.
- Pesquisa por número de contrato/pedido e cliente; pesquisa respeita autorização e não inclui texto bruto de notas/e-mails nesta fase.
- Filtros não podem alterar valores agregados sem indicação clara do escopo filtrado.

## 4. Resumo e detalhe

### Resumo de topo

Exibir indicadores somente se denominador e dataset cobertos forem conhecidos:

- quantidade de contratos ativos identificados;
- volume contratado, entregue e saldo por unidade compatível;
- quantidade de contratos com dados incompletos/em divergência/desatualizados.

Não somar unidades distintas. Se total não puder ser comparado, agrupar por unidade ou suprimir o total com explicação.

### Detalhe do contrato

- Dados comerciais canônicos e status; origem do registro.
- Quantidade vigente, produzido, entregue e saldo lado a lado, com unidade e definição acessível.
- Linha do tempo de eventos disponíveis (contratação/aditivo, evento de produção, evento de entrega/carga) ordenada por data de negócio; se a data de negócio faltar, mostrar a data de carga e marcar a ausência.
- Relações com itens de produto e registros/lotes de produção somente quando o vínculo tiver evidência.
- Alertas e divergências com descrição, valor/fonte em conflito, data e ação orientativa; não oferecer correção que altere origem.
- Referência da regra/última atualização, acessível ao champion sem sobrecarregar a leitura de usuários gerais.

## 5. Estados de interface

- **Carregando:** indicar consulta em andamento e evitar mostrar lista anterior como atual sem identificá-la.
- **Sem contratos encontrados:** diferenciar “sem resultados com estes filtros” de “nenhum contrato no conjunto carregado”.
- **Fonte desatualizada:** banner/status persistente com última atualização e consequência para números.
- **Dataset parcial:** exibir fontes/entidades incluídas e as não integradas.
- **Erro de consulta:** mensagem operacional simples, identificador para suporte e opção segura de tentar novamente; não mostrar stack trace, SQL, token ou URL privada.
- **Registro ambíguo:** avisar que existem candidatos e não consolidar detalhe como se houvesse identidade confirmada.
- **Sem permissão:** estado sem vazamento de existência ou campos protegidos.
- **Sem informação numérica:** usar estado textual, não zero.

## 6. Regras de apresentação e interação

1. Datas e números respeitam localidade pt-BR na interface, sem alterar o valor decimal original nem a unidade.
2. Valor monetário exibe moeda; se não houver moeda confiável, não converter ou mostrar símbolo presumido.
3. Saldo negativo/entrega excedente segue regra aprovada e aparece como exceção, sem limitar silenciosamente.
4. “Produzido” nunca substitui “entregue”; “previsto” nunca substitui “confirmado”.
5. Data/hora da tela indica consulta; timestamp de origem indica frescor da informação. Diferença deve ser legível.
6. Indicadores agregados são recalculados conforme filtros e informam número de registros/unidades incluídos.
7. Link da lista para detalhe mantém filtros/consulta ao retornar, quando suportado pela tecnologia escolhida.
8. Interface deve ser utilizável em desktop; responsividade mobile é desejável, mas layout mobile avançado não é critério da Fase 1 salvo decisão.
9. Conteúdo não confiável importado é exibido como texto; não interpretar HTML/script de origem.

## 7. Fora de escopo

Editar contrato na aplicação, enviar e-mail/WhatsApp, criar reserva, aprovar ordem de produção, previsão robusta, drill-down de todos os sistemas, dashboards financeiros de margem, aplicativo nativo e consulta conversacional por IA.

## 8. Critérios de aceite

- [ ] Leitor autorizado consegue abrir a lista e o detalhe de cada registro válido do conjunto homologado.
- [ ] Pesquisa e cada filtro retornam somente registros do universo autorizado e os filtros ativos são visíveis.
- [ ] Campos sem fonte ou mapeamento apresentam estado de ausência/qualidade; não são exibidos como zero ou confirmados.
- [ ] Produzido, entregue e saldo aparecem como medidas distintas e na unidade apresentada.
- [ ] Totalização de unidade mista é agrupada por unidade ou indisponível com justificativa.
- [ ] Detalhe apresenta origem, data de atualização e referências de registro/lote disponíveis.
- [ ] Atualização parcial/desatualizada e falha de consulta são distinguíveis de ausência de contratos.
- [ ] Conflito/duplicidade não aparece como registro consolidado sem indicação.
- [ ] O resumo e os resultados filtrados reconciliam com os dados publicados e a fórmula aprovada.
- [ ] Interface não permite escrita em origem nem disparo de comunicação externa.
- [ ] Dados restritos não aparecem para perfil sem permissão; tentativa negada não revela dados protegidos.

## 9. TDD e testes de interface

### Unidade/domínio

- calcular/formatar volumes por unidade sem agregação inválida;
- mapear estados de qualidade e não converter nulo em zero;
- formatar data/valor/moeda para pt-BR sem perda de precisão;
- ordenação estável e combinação de filtros;
- detalhe distingue produzido, entregue, previsão e saldo.

### Integração/componente

- lista com dados completos, parciais, vazios, divergentes e desatualizados;
- busca por número e cliente com caracteres especiais;
- paginação/limite de resultados conforme o volume piloto;
- falha e retry sem exibir resposta anterior como atual;
- autorização no servidor/API além de ocultação no front-end;
- HTML/script em texto de origem exibido com segurança;
- números de resumo reconciliam com a coleção filtrada e não misturam unidades.

### UAT

Com Comercial e Produção, executar pelo menos: contrato nacional simples; contrato internacional com entrega parcial; contrato com aditivo; contrato com falta/divergência, caso disponível; busca sem resultados; fonte desatualizada. Anotar resultado esperado, resultado observado, evidência e aceite/pendência por caso.

## 10. Desempenho e disponibilidade

O conjunto piloto citado tinha aproximadamente 4.500 linhas de contratos numa extração. Essa observação não constitui limite superior. Implementação deve suportar o volume piloto sem travar a interação, usar paginação/consulta limitada quando necessário e evitar carregar arquivo inteiro no navegador. Alvo numérico de latência e volume futuro será definido depois de confirmar infraestrutura e perfil de uso; não criar SLA neste escopo sem medição.

## 11. Riscos e recuperação

- Sem dados publicados: explicar que não há dataset ativo e orientar contato com champion, sem exibir erro técnico.
- Fonte atrasada: manter timestamp e banner; não manter o status anterior como atual sem aviso.
- Resultado de filtro inesperado: permitir limpar filtros e registrar critérios ativos; filtros não alteram a base.
- Dado corrupto ou renderização anômala: falhar fechado para campo afetado e manter visível a qualidade do registro.
- Erro de autorização: retornar resposta neutra e registrar evento de segurança sem expor consulta/dados em log amplo.

## 12. Dependências e handoff

Entradas: campos/regra homologados, carga piloto e critérios de autorização; decisões de identidade visual e stack podem ser aplicadas sem alterar regra de domínio.
Saídas: lista/detalhe demonstráveis e roteiro UAT preenchível.
Próxima: SPEC-F1-04 para consolidar autorização, revisão de segurança e liberação do incremento.
