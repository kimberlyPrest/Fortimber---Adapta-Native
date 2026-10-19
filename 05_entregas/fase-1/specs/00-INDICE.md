# Fase 1 — Índice de SPECs

## Objetivo da fase

Entregar uma primeira visão confiável dos contratos ativos e seus saldos em uma aplicação única, usando um conjunto-piloto reconciliado e deixando visíveis fonte, atualização, ausência e divergência. A fase não presume que todos os sistemas externos estarão integrados.

## Ordem recomendada

| Ordem | SPEC | Entrega | Dependências principais |
|---|---|---|---|
| 1 | [SPEC-F1-01 — Contrato de dados e regras do saldo](SPEC-F1-01-contrato-de-dados-e-saldo.md) | Dicionário aprovado, amostra-piloto, chaves, unidades, regras e referência de reconciliação. | Participação de Comercial, Produção, Champion e patrocinador. |
| 2 | [SPEC-F1-02 — Carga-piloto, validação e proveniência](SPEC-F1-02-carga-piloto-e-proveniencia.md) | Contratos e eventos selecionados carregados de forma controlada e auditável, com erros e duplicidades visíveis. | SPEC-F1-01; fonte/arquivo e arquitetura de persistência aprovados. |
| 3 | [SPEC-F1-03 — Painel e detalhe de contratos](SPEC-F1-03-painel-e-detalhe-de-contratos.md) | Lista filtrável e detalhe de contrato com quantidades, datas, saldo, status de dado e proveniência. | SPEC-F1-01 e dataset piloto válido da SPEC-F1-02. |
| 4 | [SPEC-F1-04 — Acesso, segurança e aceite operacional](SPEC-F1-04-acesso-seguranca-e-aceite.md) | Acesso autorizado, controles de leitura, validação UAT e critérios de liberação. | Política de identidade e acesso fornecida pela Fortimber; integra todas as demais SPECs. |

## Definições comuns

- **Contrato ativo:** definição a ser aprovada em SPEC-F1-01; não inferir apenas pela presença de saldo.
- **Volume entregue:** considerar somente os eventos e marco de entrega aprovados em SPEC-F1-01. Se não existir fonte confiável, exibir “não disponível”.
- **Saldo:** fórmula documentada e homologada em SPEC-F1-01; não calcular para unidades incompatíveis, quantidade ausente ou divergência sem classificar o dado.
- **Atualização:** mostrar a data/hora do último dado de origem carregado, separada da data/hora em que a tela foi consultada.
- **Ações de escrita:** a aplicação é somente leitura para os sistemas de origem na Fase 1. Importar para a camada analítica não altera sistemas fonte.
- **Decisões bloqueadoras:** ambiente/alvo de implantação, mecanismo de autenticação/autorização, fontes-piloto e definição de campos e cálculo de saldo devem ser confirmados antes da tarefa de implementação que deles depende.

## Critério global de conclusão

Champion e representantes de negócio percorrem os casos de aceite com registros piloto nacionais e internacionais, aprovam (ou registram divergências conhecidas) e confirmam que os indicadores não apresentam valores ausentes, estimados ou desatualizados como fatos confirmados. Nenhuma métrica de cobertura deve ser divulgada sem denominador aprovado.
