# SPEC-F2-04 — Regras de negócio aprovadas (D-F1-01 a D-F1-07)

**Fase:** 2 — Onda 0 (estabilização)
**Status:** planejada
**Dono:** Patrocinador Fortimber (Vinícius e Fernanda Belluzzo)
**Origem no escopo:** Escopo §12 itens 2–4; SPEC-F1-01 §7 (D-F1-01 a D-F1-07)
**Degrau da solução:** reuso — as premissas já implementadas no sistema servem de proposta inicial; o registro fica num documento do repo do cliente, sem ferramenta nova.

## Resultado observável

O documento `03_documentos/decisoes-de-dominio.md` registra, para cada uma das sete decisões, a resposta aprovada, o aprovador, a data e, quando a resposta difere da premissa implementada, a mudança necessária e a SPEC afetada. A amostra real do piloto fica escolhida e identificada só por códigos.

## Limites e dependências

- **Inclui:** as sete decisões; seleção da amostra; matriz de fonte preferencial por campo (D-F1-06), entregue em formato que o Champion consiga cadastrar (SPEC-F2-07).
- **Fora de escopo:** mudança de código (feita nas SPECs técnicas); definição de margem e resultado financeiro.
- **Entradas e pré-condições:** premissas atuais do sistema (tabela abaixo); extração de contratos da área de dados (~4.500 linhas) para escolher a amostra.
- **Saídas/artefatos:** `03_documentos/decisoes-de-dominio.md`; lista da amostra (códigos e categoria, sem dados pessoais) em `03_documentos/amostra-piloto.md`; matriz D-F1-06.
- **Dependências e responsáveis:** Comercial, Produção e Champion participam; o patrocinador aprova.
- **Risco e plano B:** decisão não tomada no prazo → a premissa atual continua valendo, marcada como "premissa não aprovada" no documento e na tela; o indicador que depende dela aparece como "aguardando definição" (SPEC-F2-14).
- **Rollback ou reversão:** decisão nova substitui a anterior com nova data; o histórico é preservado no documento.

## Fluxo e regras

| Decisão | Premissa hoje no sistema | Pergunta a fechar | Quem aprova |
|---|---|---|---|
| D-F1-01 Contrato ativo | `vigente` e `suspenso` contam como ativo | Quais estados e qual regra de data (início, prazo) definem ativo? | Patrocinador, ouvido o Comercial |
| D-F1-02 Volume entregue | `expedicao` e `entrega_confirmada` contam; `producao` à parte; `devolucao` com sinal negativo | Qual marco vale para o mercado nacional e para a exportação? O faturamento (Omie) conta como entrega? | Patrocinador |
| D-F1-03 Aditivos e excedente | Aditivo não é suportado (vira colisão); excedente aparece negativo | Aditivo substitui a quantidade ou soma a ela? Excedente é aceito, com tolerância? | Patrocinador |
| D-F1-04 Unidades | Unidade da origem, sem conversão | Quais unidades existem por produto? Há tabela de conversão aprovada? | Comercial com Produção |
| D-F1-05 Amostra | Fixtures fictícias | Quais 20 a 30 contratos reais compõem a amostra? | Comercial |
| D-F1-06 Fonte preferencial | Nenhuma | Para cada campo (quantidade contratada, entregue, prazo, preço), qual fonte vale em caso de divergência? Com qual tolerância? | Comercial |
| D-F1-07 Indicadores de 95% | Nenhum | Universo, período, unidade e vínculo válido para a cobertura de contratos e para a rastreabilidade | Patrocinador |

Proposta de composição da amostra (D-F1-05), ajustável pelo Comercial: pelo menos 5 contratos nacionais, 5 internacionais, 2 com aditivo, 2 com entrega parcial, 2 com divergência conhecida entre planilha e sistema e 1 cancelado.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Sessão de decisão realizada | Sete decisões registradas, com aprovador e data | — |
| Limite | Decisão adiada | Marcada "premissa não aprovada", com novo prazo e dono | Rever na sessão seguinte |
| Falha | Resposta contradiz o código | Mudança listada com a SPEC afetada | Emenda ou SPEC nova pelo consultor |

## Checklist de execução

- [ ] Amostra selecionada e registrada só com códigos.
- [ ] D-F1-01, D-F1-02 e D-F1-03 aprovadas pelo patrocinador.
- [ ] D-F1-04 e D-F1-06 aprovadas pelo Comercial.
- [ ] D-F1-07 aprovada pelo patrocinador.
- [ ] Diferenças entre decisão e premissa listadas com a SPEC afetada.

## Critérios de aceite

- [ ] **CA-F2-04-1:** `decisoes-de-dominio.md` contém as sete decisões, cada uma com resposta, aprovador nominal e data (ou "premissa não aprovada" com prazo e dono).
- [ ] **CA-F2-04-2:** `amostra-piloto.md` lista de 20 a 30 contratos por código e categoria, sem dados pessoais.
- [ ] **CA-F2-04-3:** toda diferença entre decisão e premissa implementada está listada com a SPEC afetada.
- [ ] **CA-F2-04-4:** a matriz D-F1-06 tem, para cada campo, a fonte preferencial e a tolerância (ou "sem tolerância"), pronta para cadastro na SPEC-F2-07.

## TDD da SPEC

Entrega não técnica: roteiro de verificação.

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Estado atual | Abrir a SPEC-F1-01 §7 | Decisões sem aprovador nem data | Referência ao arquivo |
| GREEN | CA-F2-04-1 a -4 | Revisar os documentos com o consultor | Todos os campos preenchidos | Documentos no repo do cliente |
| REFACTOR/REGRESSÃO | Coerência | Conferir cada decisão contra o código e as SPECs F2-01, F2-08, F2-11 e F2-14 | Nenhuma decisão sem SPEC de destino | Lista de diferenças |

**Dados/fixtures:** extração de contratos da área de dados; atas das sessões.
**Caminhos de erro obrigatórios:** decisão adiada; aprovador ausente; conflito entre Comercial e Produção.
**Evidência exigida:** documentos versionados no repo do cliente e ata da sessão em `02_reunioes/`.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Checklist/aceite | Recorte da prova | Status |
|---|---|---|---|---|---|---|
| F2-T11 | Selecionar a amostra real de 20 a 30 contratos e registrá-la por código e categoria | Comercial Fortimber | SPEC-F2-04 | Composição mínima atendida ou justificada.<br>Sem dados pessoais no documento. | CA-F2-04-2 | A fazer |
| F2-T12 | Aprovar D-F1-01 (contrato ativo), D-F1-02 (marco de entrega e faturamento) e D-F1-03 (aditivo e excedente) | Patrocinador Fortimber | SPEC-F2-04 | Três decisões com resposta, aprovador e data.<br>Diferenças em relação à premissa listadas com a SPEC afetada. | CA-F2-04-1 (D-F1-01 a 03) e CA-F2-04-3 | A fazer |
| F2-T13 | Aprovar D-F1-04 (unidades e conversões) e D-F1-06 (fonte preferencial e tolerância por campo) | Comercial Fortimber | SPEC-F2-04 | Unidades por produto registradas.<br>Matriz por campo pronta para cadastro. | CA-F2-04-1 (D-F1-04 e 06) e CA-F2-04-4 | A fazer |
| F2-T14 | Aprovar D-F1-07 (universo, período e denominador da cobertura e da rastreabilidade) | Patrocinador Fortimber | SPEC-F2-04 | Universo, período, unidade e vínculo válido definidos. | CA-F2-04-1 (D-F1-07) | A fazer |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| | | | |
