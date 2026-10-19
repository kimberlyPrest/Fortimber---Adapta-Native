# Status do projeto

**Atualizado em:** 19/10/2026
**Fase atual:** 2 — Integrações e reconciliação das fontes prioritárias
**Estado:** Fase 2 liberada; nenhuma task iniciada.

## Fase 1 — encerrada com ressalvas

O sistema (`Fortimber/fortimber-zzniaopq4`, PocketBase via Skip) entregou lista e detalhe de contratos, carga CSV, papéis e tela de UAT. A Fase 1 foi aceita com ressalvas, que são o primeiro trabalho da Fase 2 (Onda 0):

- saldo desconta duas vezes a mesma entrega quando duas fontes a reportam (SPEC-F2-01);
- Visão Geral exibe dados fictícios (SPEC-F2-02);
- falhas de autorização e UAT sem registro de quem respondeu (SPEC-F2-03);
- regras D-F1-01 a D-F1-07 ainda são premissas, sem aprovação (SPEC-F2-04);
- arquitetura, ambientes e backup não registrados (SPEC-F2-05);
- UAT ainda não executado com amostra real (SPEC-F2-06).

A Fase 1 está arquivada em `05_entregas/fase-1/`.

## Próximos passos

1. Iniciar a leva A de `04_fase-atual/fase.md`: 25 tasks independentes, incluindo as correções de saldo, Visão Geral e acesso.
2. Agendar a sessão de decisão das regras D-F1-01 a D-F1-07 com o patrocinador e o Comercial (SPEC-F2-04).
3. A TI registra a ADR-001 e testa a restauração do backup (SPEC-F2-05).

## Bloqueios conhecidos

- Conectores só entram em produção depois da Onda 0 e com backup testado.
- Acesso de leitura ao banco da área de dados para o conector depende da TI (F2-T32).
- Siscomex, sistema do porto, SISAP e Dolphin dependem de resposta dos fornecedores (SPEC-F2-15).

Nenhuma aprovação funcional ou liberação para produção é inferida pelo estado deste repositório.
