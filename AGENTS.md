# Orientações do repositório Fortimber

- Trate `01_projeto/02-Escopo-Definitivo.md` como o recorte global e `04_fase-atual/specs/` como contrato de comportamento da Fase 1.
- Não invente decisões marcadas como pendentes nas SPECs. Resolva-as com o responsável funcional antes de implementar o comportamento dependente.
- Faça mudanças pequenas e vinculadas a uma tarefa e SPEC; documente evidência de aceite e atualize `STATUS.md`/`changelog.md` quando uma entrega mudar de estado.
- Preserve trilha de origem, unidades e estado de qualidade dos dados. Nunca transforme dado ausente em zero ou dado estimado em confirmado.
- A Fase 1 é de leitura para sistemas de origem. Não adicione escrita, envio externo, scraping governamental ou agente com autonomia sem aprovação de escopo e segurança.
- Não grave segredos, credenciais, tokens, dados pessoais ou cópias de dados operacionais no Git. Use ambiente local protegido e dados de teste minimizados.
- Antes de concluir uma mudança, revise o diff e execute as provas relevantes à SPEC alterada; registre qualquer prova não executada.
