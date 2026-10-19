# Orientações do repositório Fortimber

- Trate `01_projeto/02-Escopo-Definitivo.md` como o recorte global e `04_fase-atual/specs/` como contrato de comportamento da fase atual.
- Não invente decisões marcadas como pendentes nas SPECs. Resolva-as com o responsável funcional antes de implementar o comportamento dependente.
- Faça mudanças pequenas e vinculadas a uma tarefa e SPEC; documente evidência de aceite e atualize `STATUS.md`/`changelog.md` quando uma entrega mudar de estado.
- Preserve trilha de origem, unidades e estado de qualidade dos dados. Nunca transforme dado ausente em zero ou dado estimado em confirmado.
- Os sistemas de origem (área de dados, Omie, Siscomex, porto, SISAP, Dolphin, Sysflora) são somente leitura. Não adicione escrita, envio externo, scraping governamental ou agente com autonomia sem aprovação de escopo e segurança.
- Respeite as levas da tabela Tasks de `04_fase-atual/fase.md`: uma task só começa quando as suas dependências estiverem concluídas.
- Integrações enviam dados pela API de ingestão com conta de serviço própria (SPEC-F2-09); nunca grave credenciais no repositório nem no código do Skip.
- Não grave segredos, credenciais, tokens, dados pessoais ou cópias de dados operacionais no Git. Use ambiente local protegido e dados de teste minimizados.
- Antes de concluir uma task, demonstre o cenário do "Recorte da prova" e anexe a evidência; o registro de testes automatizados não é obrigatório.
