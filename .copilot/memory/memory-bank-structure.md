# Estrutura do Memory Bank (Brownfield Friendly)

## Arquivos core (obrigatórios)
- `projectbrief.md` → Escopo, objetivos, stakeholders, requisitos principais.
- `productContext.md` → Problema, usuários, metas de UX, regras de negócio principais.
- `systemPatterns.md` → Arquitetura e decisões (front/back/DB), padrões e relacionamentos.
- `techContext.md` → Stack, setup, constraints, dependências (Angular/.NET/Oracle).
- `activeContext.md` → Foco atual, mudanças recentes, decisões em aberto, próximos passos imediatos.
- `progress.md` → O que funciona, o que está quebrado, issues conhecidas, status por fluxo.

## Arquivos extras (recomendados para projeto em andamento)
- `asIsInventory.md` → Inventário do sistema atual (apps, APIs, DB, integrações) com evidências.
- `contracts.md` → Contratos críticos (endpoints/DTOs, códigos de erro, integrações externas).
- `dbContext.md` → Mapa Oracle: schemas, tabelas-chave, packages/procs críticas, transações.
- `riskRegister.md` → Riscos e dívidas (ex.: .NET 3.1), com plano incremental e mitigação.

## Regras de atualização
- Ao concluir uma correção/feature: atualizar `activeContext.md` + `progress.md`.
- Ao tocar contratos/API/DB: atualizar `contracts.md`/`dbContext.md`.
- Ao descobrir componentes: atualizar `asIsInventory.md`.

