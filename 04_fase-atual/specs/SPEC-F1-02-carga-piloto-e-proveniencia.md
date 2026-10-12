# SPEC-F1-02 — Carga-piloto, validação e proveniência

**Fase:** 1
**Estado:** definição funcional; fonte-piloto e decisão de infraestrutura são pré-requisitos para implementar o adaptador.
**Dependências:** SPEC-F1-01 aprovada; destino de dados/ambientes e acesso à amostra aprovados.

## 1. Objetivo e resultado

Carregar contratos e os eventos-piloto de produção/entrega disponíveis para a camada consumida pelo painel, de modo controlado, repetível e auditável. O champion consegue saber qual arquivo ou execução produziu cada valor, quando foi atualizada e quais registros foram aceitos, rejeitados, duplicados ou mantidos em divergência.

## 2. Usuários e permissões

- **Operador de dados/champion:** pode iniciar importação e consultar relatório detalhado.
- **Leitor de negócio:** consulta dados homologados e status da atualização; não substitui nem aprova fonte.
- **Administrador técnico:** configura mapeamento e integração de leitura conforme acesso concedido.
- A identidade, autorização e privilégio são implementados conforme decisão da SPEC-F1-04. Não criar conta compartilhada ou credencial fixa em código.

## 3. Escopo funcional

### Ingestão piloto

- Suportar o formato de importação aprovado para o piloto (CSV UTF-8; XLSX se o arquivo de origem aprovado exigir múltiplas abas ou for o formato entregue). O template final fixa nomes de colunas, formato de datas, decimais, delimitador e unidade.
- Aceitar carga sob ação explícita de usuário autorizado. Fase 1 não exige agendamento ou conector periódico.
- Validar extensão/MIME/tamanho máximo configurado, codificação, cabeçalho, abas/colunas requeridas, tipos, datas, valores numéricos, chaves e unidade.
- Mostrar prévia das primeiras linhas, contagem e erros antes de confirmar a publicação da carga.
- Permitir cancelar antes da confirmação sem alterar o dataset atualmente publicado.
- Para arquivos aprovados, criar versão/lote imutável de ingestão e registrar hash/checksum ou identificador seguro equivalente para detectar reenvio idêntico.
- Manter valores originais da fonte e valores normalizados separados no armazenamento ou com proveniência equivalente.

### Idempotência e publicação

- Uma carga idêntica repetida não cria duplicatas; ela é identificada como repetida ou como nova verificação sem reaplicar eventos.
- Chaves e regras de deduplicação vêm de SPEC-F1-01. Sem chave resolvida, manter o registro em conflito para revisão.
- Validar todo o lote e preparar resultado antes de ativá-lo para o painel. Se a carga falha em validação estrutural, a versão anterior continua disponível.
- Reprocessar uma versão deve produzir o mesmo resultado sem duplicação, salvo mudança de versão de regra/mapeamento explicitamente registrada.
- Não sobrescrever carga anterior. A ativação de nova versão mantém referência suficiente para reversão à última versão homologada.

### Relatório de carga

Cada execução apresenta:

- identificador, arquivo/origem, usuário iniciador e instante de início/fim;
- período/datas de negócio cobertos quando disponíveis;
- total de linhas lidas, válidas, incompletas, rejeitadas, duplicadas e em conflito;
- validações executadas e versão de mapeamento/regra usada;
- erros agrupados por coluna/código, com referência de linha/registro e orientação de correção;
- estado final: pré-validando, pronta para aprovação, publicada, publicada com exceções, rejeitada ou cancelada;
- instante da última atualização bem-sucedida exibido ao painel.

Erros não devem revelar segredo, token, caminho privado ou dados pessoais além do mínimo necessário ao operador autorizado.

## 4. Fontes e limites

- Primeira fonte: conjunto escolhido na SPEC-F1-01, podendo ser exportação Excel/CSV atualmente usada e/ou dataset já consolidado na área de dados.
- Nesta fase, nenhuma integração externa é obrigatória se o acesso técnico ou a autorização não estiver disponível. O fluxo precisa declarar claramente sua cobertura manual.
- Omie/Siscomex/porto/Dolphin/SISAP/Sysflora não são automaticamente adicionados nesta SPEC por terem sido citados; cada fonte requer entidade, acesso e mapeamento aprovados.
- Conector de leitura já existente poderá ser chamado apenas se seu dono, esquema, atualidade, credenciais e comportamento de falha forem documentados.

## 5. Regras de validação

1. Não preencher nulo com zero, hoje, status padrão ou unidade presumida.
2. Rejeitar o lote inteiro somente para falha estrutural que torne interpretação não confiável; para erro localizado, comportamento (rejeitar linha ou manter incompleta) deve ser configurado conforme criticidade definida por SPEC-F1-01.
3. Campo/registro com dado conflitante não entra no cálculo dependente até resolução ou decisão explícita.
4. A data de carga é distinta da data do evento de negócio; ambas devem ser preservadas quando existirem.
5. Data sem fuso deve seguir convenção aprovada da Fortimber; a aplicação não inventa zona do sistema fonte.
6. Valor de moeda e número decimal usa formato interpretável sem ambiguidade; parsing deve rejeitar formatos mistos que possam mudar escala.
7. Falha ou cancelamento da nova importação não substitui a última versão publicada.
8. O arquivo original deve ser retido pelo período definido pela política de dados, com acesso limitado; retenção indefinida não é padrão.

## 6. Fluxo principal

1. Usuário autorizado escolhe fonte/template e arquivo ou lote disponível.
2. Aplicação valida formato e apresenta resumo/prévia sem publicar.
3. Usuário corrige o arquivo ou confirma publicação de registros válidos e incompletos permitidos pela regra.
4. Aplicação grava lote, itens, erros, proveniência e resultado da validação.
5. Se validação bloquear, versão publicada permanece; usuário vê causa e linhas afetadas.
6. Após publicação, painel passa a usar a versão ativa e mostra seu timestamp.
7. Operador pode abrir o relatório e comparar com lote anterior; rollback controlado restaura a última versão previamente homologada sem apagar histórico.

## 7. Segurança e integridade

- Importação limitada a usuários autorizados e tipos/tamanho definidos.
- Sanitizar nomes e conteúdo de arquivo; não executar fórmulas/macros nem interpretar conteúdo de célula como comando.
- Evitar exposição de conteúdo integral de linha em logs gerais; mostrar detalhe só a papel autorizado.
- Guardar credencial fora de arquivo importado, prompt ou repositório.
- Aplicar transação/estratégia equivalente para impedir publicação parcial inconsistente.
- Registrar ator e ação em operações de importar, publicar, cancelar e reverter.

## 8. Critérios de aceite

- [ ] Arquivo conforme template é pré-validado e não fica visível como dado publicado até confirmação explícita.
- [ ] Arquivo com cabeçalho ausente, tipo inválido ou decimal/data ambíguos é rejeitado com erros acionáveis.
- [ ] Campo opcional ausente aparece como incompleto; não se converte silenciosamente em zero ou dado fictício.
- [ ] Carga repetida não duplica contrato/evento segundo as chaves de SPEC-F1-01.
- [ ] Conflito/duplicidade é reportado com as referências de origem e não fundido sem regra aprovada.
- [ ] Erro estrutural mantém a última versão válida consultável.
- [ ] O relatório de lote apresenta contadores reconciliáveis: linhas lidas = linhas classificadas, respeitadas as linhas de cabeçalho/metadados definidas no template.
- [ ] Cada registro exibido permite consultar lote/fonte, chave original, instante e regra de transformação.
- [ ] Cancelamento e rollback deixam trilha e não apagam histórico anterior.
- [ ] Se a fonte ficar mais antiga do que a janela aprovada, painel apresenta estado desatualizado.

## 9. TDD e provas

Antes de conectar a dados reais, criar fixtures pequenas anonimizadas cobrindo:

- arquivo válido nacional e arquivo válido internacional;
- cabeçalho desconhecido, coluna requerida ausente e coluna adicional não mapeada;
- datas locais/ambíguas, data impossível e fuso ausente;
- número com separador decimal e milhar esperado, escala ambígua e valor negativo conforme regra;
- chave repetida no mesmo lote, reenvio do mesmo lote e duplicidade entre lotes;
- registro válido junto a um registro inválido, conforme política de carga parcial aprovada;
- falha após validação e antes da publicação;
- versão de mapping alterada e reprocessamento;
- cancelamento e rollback da versão ativa.

Provas unitárias: parser/normalizador, validadores, cálculo de checksum, deduplicação e política de publicação. Provas integradas: importar, revisar, publicar, consultar no painel e confirmar proveniência sem conectar/alterar sistemas externos.

## 10. Observabilidade e falhas

- Indisponibilidade de destino: marcar execução como falha, preservar arquivo conforme política e permitir repetição segura.
- Formato novo: falhar fechado; não tentar “adivinhar” colunas.
- Lote parcialmente inválido: aplicar política de linha aprovada e exibir contagens; manter relatório legível.
- Volume acima do limite: recusar antes de processar e informar limite operacional.
- Reprocessamento simultâneo: serializar ou rejeitar conflito de publicação; nunca ativar parcialmente dois lotes concorrentes.
- Arquivo malicioso ou com fórmula: não executar conteúdo; rejeitar padrão não suportado e registrar código seguro.

## 11. Decisões técnicas necessárias

- destino de persistência e ambiente de homologação;
- formato piloto efetivamente entregue e limite de tamanho;
- tecnologia de parser suportada pelo stack aprovado;
- política de retenção do original e dos erros detalhados;
- frequência/janela que define “desatualizado”;
- decisão de aprovação manual para publicar carga (recomendada para o primeiro piloto).

## 12. Dependências e handoff

Entradas: SPEC-F1-01 aprovada, acesso a ambiente, formato/template, decisão de persistência, perfil de operador, dados de teste minimizados.
Saídas: dataset piloto publicado, histórico de carga, template e catálogo de erros.
Consumidores: SPEC-F1-03 e SPEC-F1-04.
