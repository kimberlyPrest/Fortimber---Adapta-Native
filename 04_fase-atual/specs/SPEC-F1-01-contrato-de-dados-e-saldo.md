# SPEC-F1-01 — Contrato de dados e regras do saldo

**Fase:** 1 — Painel confiável de contratos ativos e saldos
**Estado:** pronta para validação funcional; implementação bloqueada até fechar as decisões listadas abaixo.
**Responsáveis de negócio esperados:** Comercial/contratos, Produção/indústria e patrocinador.
**Champion técnico:** Fábio Pimentel.

## 1. Objetivo e resultado

Definir o vocabulário canônico, as chaves e as regras que permitem consolidar uma amostra de contratos sem misturar unidades nem criar saldos incorretos. Ao final, Fortimber deve ter aprovado uma tabela de mapeamento entre campos de origem e campos canônicos, junto a casos de exemplo cujos resultados esperados foram calculados e conferidos manualmente.

## 2. Usuário e necessidade

Comercial/contratos precisa localizar um compromisso de venda e saber os volumes contratados, reconhecidos como entregues e ainda pendentes. Produção precisa confirmar que o produto e o volume associados são comparáveis ao compromisso. O champion precisa rastrear de qual arquivo/sistema e registro veio cada valor.

## 3. Escopo da SPEC

- Selecionar o conjunto-piloto de contratos ativos: incluir casos nacionais e internacionais, contratos com saldo, casos concluídos/cancelados úteis para testar estados, aditivos e ao menos um caso com dado faltante ou divergente, se existirem.
- Inventariar campos e identificadores das fontes do piloto (Excel/Power BI e fonte de produção/entrega que realmente estará disponível).
- Criar dicionário dos campos canônicos descritos abaixo, registrar fonte preferencial por campo, tipo, nulabilidade, unidade, regra de transformação e responsável funcional pela definição.
- Definir status e fórmula de saldo com exemplos assinados pelos responsáveis funcionais.
- Definir regra de identidade de contrato e ligação entre contrato, pedido, aditivo, item e cliente, preservando chaves externas.
- Definir unidade comercial e qualquer conversão autorizada, precisão e arredondamento; não misturar volume, peças, peso ou unidades logísticas.
- Documentar limitações e mapeamentos sem correspondência.

## 4. Modelo lógico mínimo

O modelo abaixo é conceitual e não determina tabelas, ORM ou tecnologia física.

### Contrato/pedido

- Identificador interno estável (gerado pela aplicação ou camada de dados, conforme arquitetura aprovada).
- Sistema e identificador externo de origem; empresa/filial, se presentes.
- Número de contrato e/ou pedido como exibido na origem; não usar como chave única sem testar colisões.
- Mercado (nacional/internacional), cliente/contraparte, trade/intermediário quando aplicável.
- Estado de origem e estado canônico, data de início/vigência, prazo ou janela de entrega.
- Moeda, condição comercial e preço unitário quando a amostra permitir; esses valores são exibidos com fonte e moeda, não usados para inferir margem.
- Estado de registro (vigente, encerrado, cancelado, suspenso ou outro vocabulário aprovado) e vínculo com aditivo/versão.

### Item contratual

- Produto e especificação comercial; espécie quando disponível; identificadores de origem e mapeamento canônico.
- Quantidade vigente e unidade de medida da origem.
- Quantidade reconhecida como entregue segundo o evento/marco aprovado.
- Saldo em unidade compatível ou estado “não calculável”.
- Datas e referências de evidência de produção/entrega associadas, quando disponíveis.

### Proveniência associada

- Fonte, identificador do registro, arquivo/lote ou execução de integração, data/hora extraída e data/hora carregada.
- Estado de validação; erros e observações sem sobrescrever valor original.
- Para campo derivado: regra/versão do cálculo.

## 5. Regras de negócio a homologar

1. **Identidade:** dois registros só representam o mesmo contrato quando uma chave composta/regra aprovada os identifica. Uma colisão fica como exceção, não é mesclada silenciosamente.
2. **Aditivos:** identificar se alteram quantidade, preço, prazo, produto, entrega parcial ou cancelamento. A versão vigente deve ser calculável sem apagar os valores anteriores.
3. **Contrato ativo:** negócio define quais estados e datas tornam o contrato ativo no painel. Contrato encerrado/cancelado não entra na lista ativa, mas pode continuar consultável se autorizado.
4. **Quantidade contratada vigente:** obter da versão/itens válidos segundo a regra de aditivo aprovada. Se o sistema não permitir determinar a vigência sem interpretação humana, marcar como ambígua.
5. **Entregue:** somente eventos aprovados (ex.: expedição, embarque, entrega confirmada ou outro marco) contam. Os mercados nacional e internacional podem adotar marcos distintos; cada um precisa de definição explícita.
6. **Saldo:** `quantidade contratada vigente − quantidade reconhecida como entregue`, usando a mesma unidade e regra de sinal/retorno aprovada. Não limitar a zero se excedente de entrega precisa ser visível; apresentar a exceção conforme decisão de negócio.
7. **Produzido:** não é equivalente a entregue. Volume produzido deve ser exibido separadamente e só pode se ligar ao contrato quando houver evidência de vínculo ou regra de alocação aprovada.
8. **Preço:** preservar valor, moeda e unidade como registrados. Conversão cambial ou totalização em moeda-base fica fora da Fase 1, salvo decisão explícita.
9. **Ausência ou divergência:** nulo, unidade desconhecida ou fontes conflitantes causam estado não calculável/em divergência; não preencher com zero nem escolher uma fonte sem regra.
10. **Estoque e disponibilidade:** se não houver dado confiável nesta fase, exibir “não disponível nesta fase/fonte”, sem reaproveitar o saldo contratual como se fosse disponibilidade.

## 6. Decisões que precisam ser registradas antes de implementar cálculos

| ID | Decisão | Sugestão de resolução | Bloqueia |
|---|---|---|---|
| D-F1-01 | O que caracteriza contrato ativo? | Aprovar lista de estados e regra de data/vigência com Comercial. | Filtros, totais e KPI de cobertura. |
| D-F1-02 | Qual evento conta como volume entregue em cada mercado? | Aprovar marco e fonte, podendo haver regra diferente nacional/exportação. | Fórmula de saldo. |
| D-F1-03 | Como aditivos e entregas excedentes alteram quantidade vigente/saldo? | Homologar casos reais com histórico. | Fórmula de saldo e auditoria. |
| D-F1-04 | Quais unidades e conversões são válidas por produto? | Usar unidades da origem até tabela aprovada. | Comparação e agregação de volume. |
| D-F1-05 | Que amostra e período são representativos? | Selecionar contratos reais nacionais/internacionais, anonimizando cópias para desenvolvimento quando possível. | UAT e cálculo de completude. |
| D-F1-06 | Quem é fonte preferencial por campo e como resolver divergência? | Aprovar matriz por entidade/campo; preservar ambos valores em conflito. | Consolidação. |
| D-F1-07 | Como medir os 95%? | Definir universo, período, unidade e vínculo válido; reportar numerador/denominador. | Publicação do KPI. |

## 7. Fluxo funcional

1. Champion reúne esquema/arquivos de origem e amostra aprovada.
2. Responsáveis funcionais associam campos de origem aos campos canônicos.
3. Champion apresenta casos esperados: simples, aditivo, entrega parcial, encerrado/cancelado, unidades incompatíveis, ausente e divergente, conforme dados existentes.
4. Negócio decide regras e assina os valores esperados; unresolved permanece identificado como exceção e bloqueia cálculo correspondente.
5. Mapeamento versionado é entregue à SPEC-F1-02 para construção da carga e à SPEC-F1-03 para apresentação.

## 8. Fora de escopo

Definir o modelo completo de floresta, produção, estoque, cadeia de custódia, custos, contabilidade, câmbio ou margem; alterar registros da origem; decidir automaticamente a fonte “correta” durante conflito; preencher campo por IA; deduzir unidade/produto por similaridade não revisada.

## 9. Critérios de aceite

- [ ] Amostra inclui contratos nacionais e internacionais selecionados pelos donos de negócio, com lista de casos e período registrados.
- [ ] Dicionário de dados inclui campo canônico, campo de origem, tipo/unidade, nulabilidade, transformação, fonte e responsável.
- [ ] Chave e regra de aditivo foram exercitadas contra a amostra; colisões/ambiguidade têm tratamento explícito.
- [ ] Fórmula de ativo, contratado vigente, entregue e saldo está aprovada e tem pelo menos um caso calculado por tipo de exceção encontrado.
- [ ] Produzido está conceitualmente separado de entregue; estoque/disponibilidade não é falsamente inferido nesta fase.
- [ ] Numerador e denominador da meta inicial de rastreabilidade estão definidos ou a métrica está explicitamente bloqueada para publicação.
- [ ] Decisões D-F1-01 a D-F1-07 estão registradas com aprovador e data; itens não aplicáveis têm justificativa.

## 10. TDD / provas executáveis derivadas

Antes da implementação da regra de saldo, criar exemplos de entrada e saída aprovados para:

1. Contrato simples com quantidade e entrega na mesma unidade → saldo esperado.
2. Contrato com entrega parcial → saldo ainda positivo esperado.
3. Aditivo que aumenta/reduz quantidade → saldo conforme versão vigente esperada e histórico preservado.
4. Evento de entrega em unidade incompatível → saldo não calculável e erro de unidade esperado.
5. Contrato duplicado/ambíguo → carga rejeita ou sinaliza duplicidade, sem mescla silenciosa.
6. Volume produzido maior que entregue → os campos permanecem separados.
7. Ausência de quantidade/evento → não calcular e não converter ausência para zero.
8. Excedente de entrega, cancelamento e devolução → comportamento conforme decisão registrada.

As provas devem rodar contra função de domínio sem conexão externa; evidência de UAT compara a amostra homologada com a visualização final.

## 11. Falhas, recuperação e riscos

- Arquivo sem cabeçalho/mapeamento esperado: rejeitar importação daquela versão, explicar coluna/linha e manter dataset anterior ativo.
- Campo obrigatório não mapeado: carregar o registro como incompleto somente se negócio aprovar essa visibilidade; nunca incluir em total que exige o campo.
- Contrato duplicado ou aditivo ambíguo: separar em exceção com referências, sem deduplicação automática não aprovada.
- Regra alterada após carga: registrar versão, recalcular apenas em ambiente controlado e comparar resultado antes de ativar.
- Amostra com dados pessoais/comerciais: limitar cópias, mascarar quando possível e autorizar acesso antes do uso em desenvolvimento.

## 12. Dependências e handoff

Entradas: amostras de contrato/pedido/entrega e arquivos/campos reais; responsáveis funcionais; decisões D-F1-01 a D-F1-07; decisão técnica de onde persistir configuração/dicionário.
Saídas: dicionário aprovado, regras versionadas, fixtures de teste anonimizadas e relação de exceções.
Próxima: SPEC-F1-02 (carga) e SPEC-F1-03 (painel).
