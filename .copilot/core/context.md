# Contexto Operacional do Copilot (Projeto Novo / Padaria)

Você está atuando em um **projeto novo** de sistema para padaria.
Seu objetivo é maximizar **acurácia** e **segurança de mudança**: construir com rastreabilidade.

## Stack confirmada
- **Frontend**: React
- **Backend**: Java Spring Boot
- **Banco**: PostgreSQL
- **Migrações**: Flyway
- **Segurança**: Spring Security

## Verdade e evidência
- **Fonte de verdade**: arquivos do repositório + Memory Bank (`.copilot/memory/`) + documentos em `/docs`.
- **Nunca invente** contratos, regras, tabelas, payloads ou fluxos.
- Toda afirmação relevante deve apontar **evidência** (caminho do arquivo, nome de classe, endpoint, procedure, etc.).

## Rotina obrigatória antes de qualquer alteração relevante
1. **Inventário As-Is (rápido, objetivo)**
   - Identificar: apps React, APIs Java, jobs, libs compartilhadas, integrações externas, postgresql (schemas/packages).
   - Mapear pontos de entrada: rotas react, usar componentes sempre e reusá-los, controllers/endpoints, stored procedures críticas.
2. **Reprodução**
   - Tentar reproduzir falhas com passos mínimos (log, print, request sample).
3. **Baseline**
   - Registrar em `progress.md`: o que está quebrado, como reproduzir, impacto, prioridade.
4. **Mudança mínima segura**
   - Preferir correções pequenas e verificáveis (diff pequeno, testes/validação).
5. **Atualização do Memory Bank**
   - Atualizar `activeContext.md` e `progress.md` sempre que concluir um bloco de trabalho.

## Estrutura de referência
- `.copilot/core/` → identidade, regras, padrões obrigatórios.
- `.copilot/memory/` → Memory Bank (continuidade entre sessões).
- `.copilot/workflows/` → Plan / Act / Retry / Learning / Task Logs.
- `.copilot/diagrams/` → fluxos mermaid.

