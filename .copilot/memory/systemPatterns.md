# System Patterns

## Visão geral
Sistema web para controle de padaria de pequeno porte. Arquitetura em camadas: frontend SPA + backend API REST + banco relacional.

## Front (React)
- SPA com React
- Interface simples: poucos cliques, botões grandes, linguagem cotidiana
- Perfis de acesso: atendente, caixa, dono/gerente, cozinha
- Validação de UX (não substitui backend)
- Consumo de contratos OpenAPI/DTO

## Backend (Java Spring)
- Java Spring Boot
- Controllers finos, regras em Services
- Validação de DTOs
- Padrão de erro: `{ code, message, details }`
- Logs estruturados + correlation-id
- Spring Security para autenticação/autorização

## Integração/Contratos
- API REST (JSON)
- Contratos definidos antes da implementação
- Breaking changes com plano de migração

## PostgreSQL
- Flyway para migrações
- Entidades e campos em minúsculo
- Funções/procedures documentadas
- Transações explícitas

## Padrões
- SOLID, DDD, Padrões de Projeto
- Testes unitários obrigatórios para lógica crítica
- 6 módulos: Cadastros, Estoque/Compras, Atendimento/Pedidos, Caixa/Financeiro, Operação/Controle, Relatórios
