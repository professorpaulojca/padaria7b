# Regras Obrigatórias (Alta Acurácia • Brownfield)

Estas regras são **não negociáveis**.

## 1) Evidência antes de opinião
- Antes de propor mudança, **localize a evidência** no repo (arquivo/classe/rota/procedure).
- Se a evidência não existir, registre como **DÚVIDA** e não “complete” por suposição.

## 2) Plano antes de implementar (quando passar de 30 min ou mexer em mais de 2 arquivos)
- Sempre produzir um mini-plano no workflow **Problem Analysis & Planning**.
- O plano deve listar: escopo, arquivos tocados, riscos, testes/validação.

## 3) Mudança mínima, segura e verificável
- Prefira **diff pequeno** e reversível.
- Não fazer refactor grande “aproveitando a viagem” sem task explícita.

## 4) Contratos primeiro (front/back/DB)
- Se houver API, o contrato deve ser explícito (DTOs, status codes, validações, erros).
- Se houver Oracle/PLSQL, documentar:
  - package/procedure, assinatura, parâmetros, exceções, transação/commit/rollback esperado.

## 5) Sem placeholders e sem “TODO”
- Nada de `TODO`, `implement later`, “placeholder” ou código propositalmente quebrado.
- Se algo não puder ser finalizado, registrar **bloqueio** + proposta objetiva em `activeContext.md`.

## 6) Não quebrar compatibilidade sem registrar
- Mudanças breaking precisam:
  - Nota no `activeContext.md`
  - Plano de migração (front/back/DB) e validação.

## 7) Segurança e dados acima de estilo
Prioridade: **segurança → corretude → dados → runtime → testes → lint/estilo**.

## 8) Atualização obrigatória do Memory Bank
Ao finalizar um bloco de trabalho:
- `activeContext.md`: o que mudou + decisões + próximos passos
- `progress.md`: status, falhas corrigidas, falhas abertas, evidências

## 9) Quando houver falha/risco alto, usar Retry workflow
- Se não conseguir reproduzir/entender, **pare**, use `retry.md`, reduza o problema e volte com evidência.

## 10) Criar documentação baseada na UML, Diagramas de Atividades, Diagrama de Implantação, BPMN usando Apache Camunda
- Sempre criar os diagramas dos fluxos, sem distinção