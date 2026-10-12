# SPEC-F1-04 — Acesso, segurança e aceite operacional

**Fase:** 1
**Estado:** requisitos de segurança definidos; configuração final depende da identidade, ambiente e política fornecidos pela Fortimber.
**Dependências:** decisões de ambiente/identidade; SPEC-F1-01 a SPEC-F1-03.

## 1. Objetivo

Permitir que usuários designados consultem os contratos-piloto sem expor dados a usuários não autorizados, proteger credenciais e manter evidência suficiente para explicar atualizações e decisões. A aplicação da Fase 1 é somente leitura para sistemas de origem.

## 2. Atores de segurança

- **Leitor funcional:** leitura do universo expressamente autorizado.
- **Operador de dados:** inicia cargas e lê relatórios de validação.
- **Administrador técnico:** administra configuração, integração e acessos conforme delegação da Fortimber.
- **Aprovador funcional:** valida regra e conjunto de dados, sem necessariamente possuir acesso administrativo.

Não se assume que nomes de perfis correspondam a grupos reais de identidade. Fortimber deve fornecer usuários/grupos e aprovar a matriz de autorização.

## 3. Matriz de capacidades mínima

| Capacidade | Leitor funcional | Operador de dados | Administrador | Aprovador funcional |
|---|---:|---:|---:|---:|
| Consultar lista/detalhe autorizado | Sim | Sim | Sim | Sim |
| Ver proveniência operacional detalhada | Conforme aprovação | Sim | Sim | Conforme aprovação |
| Enviar arquivo de importação | Não | Sim | Sim | Não, salvo acumular papel |
| Publicar carga | Não | Conforme decisão Fortimber | Sim ou aprovador separado | Conforme decisão Fortimber |
| Alterar mapeamento/regra | Não | Não por padrão | Sim, com revisão/aprovação funcional | Aprovar regra |
| Alterar sistema de origem | Não | Não | Não pela Fase 1 | Não |
| Enviar comunicação externa | Não | Não | Não | Não |

Separação entre quem prepara e quem aprova uma publicação deve ser decidida conforme risco e tamanho do piloto. Se o cliente exigir segregação, implementá-la como controle obrigatório.

## 4. Controles obrigatórios

- Autenticação individual através de mecanismo aprovado (SSO/OAuth ou identidade existente, conforme disponibilidade); não criar senha genérica compartilhada.
- Autorização aplicada no serviço/API/dados, não apenas escondendo botões no front-end.
- Princípio do menor privilégio: somente campos e ações necessários para a função.
- TLS em trânsito e criptografia em repouso conforme recursos do ambiente escolhido; confirmar responsabilidade de configuração e gestão de chaves.
- Segredos em secret manager/variáveis protegidas próprias do ambiente; nunca no código, arquivo de configuração versionado, log ou prompt de agente.
- Logs de autenticação, importação, publicação, rollback, mudança de regra e negação de acesso sem registrar token, senha ou payload comercial integral sem necessidade.
- Sessão expira conforme política corporativa e tem logout; política de MFA/SSO deve seguir o padrão Fortimber.
- Dados de teste minimizados/mascarados; acesso a dataset real é concedido por necessidade de trabalho.
- Backup/restore e retenção definidos para o ambiente; nunca assumir retenção indefinida.
- Erro de sistema para usuário não expõe stack trace, SQL, caminho local, host privado, chave ou credencial.

## 5. Limites de dados e IA

- Fase 1 não usa IA generativa para calcular saldo ou inferir status.
- Não conectar ferramentas externas ou assistentes a dados reais sem aprovação de finalidade, provedor, escopo de dados, região/tratamento e autorização.
- Nenhuma senha rotativa de portal governamental deve ser copiada para chat, código, planilha de projeto ou repositório.
- A camada de dados expõe somente as colunas necessárias à aplicação. Views read-only são preferíveis para consumo, sujeitas à decisão de arquitetura.
- Mudanças de escopo de acesso ou integração são registradas e revisadas por Fortimber.

## 6. Checklist de pré-go-live

- [ ] Ambiente de produção/homologação identificado e responsável técnico nomeado.
- [ ] Usuários/grupos e matriz de permissão validados por dono do dado.
- [ ] Nenhuma credencial/segredo presente no repositório, no bundle cliente ou em logs da prova.
- [ ] Acesso indevido a rota/API/dado foi testado no servidor, além da interface.
- [ ] TLS e proteção em repouso habilitados conforme o ambiente e evidenciados.
- [ ] Logs/auditoria podem explicar quem publicou carga, qual versão e quando, sem excesso de dado sensível.
- [ ] Backup e procedimento de restauração testados ou aceite formal de risco registrado para piloto controlado.
- [ ] Dados reais em desenvolvimento estão autorizados e minimizados; cópia de teste está mascarada quando possível.
- [ ] Fonte e política de atualização estão documentadas; integração permanece read-only.
- [ ] Critérios de aceite funcional da Fase 1 foram percorridos e evidências arquivadas.
- [ ] Pendências/riscos residuais têm dono, prazo ou decisão de não bloquear o piloto.
- [ ] Fortimber aprova explicitamente go-live/piloto e usuários participantes.

## 7. Roteiro de aceite humano (UAT)

O champion prepara o ambiente homologado, registros da amostra e resultado esperado. Representantes funcionais executam cada cenário e registram aprovado/reprovado, evidência e observação. Falha de cálculo não deve ser classificada como problema visual; direcionar à regra da SPEC-F1-01. Falha de carga/proveniência direciona à SPEC-F1-02. Falha de exibição/filtro direciona à SPEC-F1-03.

| Cenário | Ação | Resultado esperado | Evidência mínima |
|---|---|---|---|
| Contrato nacional simples | Buscar e abrir contrato da amostra. | Valores e unidade iguais à regra aprovada; origem e atualização presentes. | Captura/export seguro e ID de caso. |
| Contrato de exportação com entrega parcial | Abrir o contrato e verificar eventos. | Produzido separado de entregue; saldo conforme marco decidido. | Caso esperado/observado e aprovador. |
| Aditivo | Abrir contrato alterado. | Versão vigente correta, histórico/proveniência preservados. | Referência ao aditivo. |
| Campo obrigatório ausente | Abrir registro incompleto. | Estado não calculável/ausente, sem valor zero inventado. | Registro de origem e resultado. |
| Duplicidade/conflito | Consultar registro sinalizado. | Não há mescla silenciosa; referências conflitantes visíveis ao perfil permitido. | ID dos registros conflitantes. |
| Carga inválida | Importar arquivo de teste rejeitável. | Versão publicada anterior permanece ativa; erro acionável é apresentado. | ID da execução e contadores. |
| Acesso negado | Usuário sem papel tenta acessar rota/dado. | Dados não são retornados nem existência confirmada quando restrita. | Resultado do teste de autorização sem dados sensíveis. |
| Fonte desatualizada | Consultar dataset além da janela aprovada. | Banner/estado de desatualização, com timestamp e impacto. | Timestamp e janela usada. |

## 8. Critérios de aceite da SPEC

- [ ] Matriz de papel/permissão aprovada por Fortimber e configurada no serviço e na interface.
- [ ] Teste negativo prova que perfil sem acesso não consegue ler dado diretamente por API/rota.
- [ ] Fase 1 não grava em fonte operacional nem envia comunicação externa.
- [ ] Busca de segredos em repositório/build/log não encontra credencial ativa.
- [ ] Operações de carga/publicação e alterações administrativas deixam trilha de ator e instante.
- [ ] Checklist pré-go-live completo ou pendência e aceitação de risco registradas pelo aprovador apropriado.
- [ ] UAT executado por pelo menos um representante de Comercial e um de Produção, além do champion; substituições precisam ser registradas.
- [ ] Aprovador da Fortimber registra go-live do piloto ou lista explícita de bloqueios; nenhuma aprovação é inferida por silêncio.

## 9. TDD / provas

- Testes unitários de política de autorização para cada papel e capacidade.
- Teste integrado de tentativa de acesso direto a recurso com papel insuficiente.
- Teste de sessão expirada e logout.
- Teste que operações de publicação/rollback geram ator/instante e que log não inclui segredo/payload integral.
- Teste de que o caminho de Fase 1 não possui endpoint/mutação para fonte operacional nem rotina de envio externo.
- Teste de exposição de erro genérico ao usuário e detalhe técnico somente em log restrito.
- Roteiro manual UAT conforme tabela da seção 7 com evidências minimizadas.

## 10. Tratamento de falhas e incidentes

- Falha de autenticação: negar acesso, não degradar para sessão anônima com dado real.
- Serviço de identidade indisponível: falhar fechado para leitura protegida; apresentar indisponibilidade sem usar credencial alternativa oculta.
- Suspeita de vazamento de segredo: interromper credencial afetada e seguir processo Fortimber de rotação/incidente; não registrar o valor na ocorrência.
- Restauração de backup: restaurar em ambiente isolado, conferir consistência e só então reabrir tráfego conforme autorização.
- Erro de autorização ou dado exposto: suspender acesso ao endpoint/tela afetado até correção e repetir prova de acesso negativo.

## 11. Decisões pendentes

- Provedor/serviço de autenticação existente e política MFA/SSO.
- Se cada filial/entidade exige segregação por usuário.
- Quem pode publicar uma importação, alterar regras e reverter lote.
- Ambiente alvo, responsabilidades de TLS, criptografia, chaves, backup, retenção e monitoramento.
- Regras de retenção para arquivos originais, dados consolidados, logs e evidência UAT.
- Quem dá aceite funcional e quem autoriza piloto em produção.
