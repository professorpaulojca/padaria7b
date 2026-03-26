# Constitution – Regras do Projeto

## Princípios
1. SPEC governa o código
2. Contrato antes da implementação
3. Mudança mínima segura
4. Evidência antes de opinião

## Stack Confirmada
- **Frontend**: React
- **Backend**: Java Spring
- **Banco**: PostgreSQL
- Decisão registrada em: DP-STACK-001 (resolvida)

## Frontend (React)
- Validação de UX não substitui validação de backend
- Consumo estrito de contratos (OpenAPI/DTO)

## Backend (Java Spring)
- Nenhuma lógica crítica sem teste mínimo
- Logs estruturados + correlation-id

## Banco (PostgreSQL)
- Toda função/procedure documentada (assinatura, parâmetros, exceções)
- Transação explícita (commit/rollback)
- Índices e locks documentados

## Qualidade
- Nenhuma task sem critério de aceite
- Nenhuma implementação fora do tasks.md