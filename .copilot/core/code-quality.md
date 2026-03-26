# Padrões Modernos de Qualidade (react • Java Spring Boot • Postgresql)

## Objetivo principal
Maximizar **corretude, previsibilidade e rastreabilidade** em um projeto já existente.

## Regras de qualidade (ordem de prioridade)
1. **Corretude e integridade de dados**
   - Validar entradas (backend como guardião final).
   - Evitar alterações que mudem significado de dados sem migração.
2. **Segurança**
   - Autenticação/autorização consistentes.
   - Nunca logar segredos; cuidado com PII.
3. **Observabilidade**
   - Backend: logs estruturados + correlation-id (request → DB).
   - Front: tratamento de erros e mensagens consistentes.
4. **Contratos e compatibilidade**
   - DTOs e códigos de erro padronizados.
   - Breaking changes apenas com plano/migração.
5. **Testabilidade**
   - Cada correção relevante deve ter validação (unit/integration/e2e ou reprodução documentada).
6. **Performance (após corretude)**
   - Oracle: índices/joins e planos de execução quando necessário.
   - Evitar otimização prematura; medir antes.

## Angular (prático)
- Evitar duplicação de regra: UX valida, backend confirma.
- Centralizar erros de API em interceptor + padrão de mensagem.
- Preferir typed forms (quando possível) e contratos consistentes.

## Java Spring Boot (prático)
- Controllers finos, regras em services.
- Validar DTOs (DataAnnotations/FluentValidation se existir).
- Retornos consistentes (ProblemDetails ou padrão equivalente).
- Logging: incluir request-id/correlation-id.
- Log das ações tanto no backend quanto no frontend.
- Usar Java Moderno e funções modernas do Java que esta configurado.

## Postgresql (prático)
- Documentar packages/procs tocadas (assinatura + exceções).
- Cuidado com commits dentro de procedures (registrar comportamento).
- Se houver lentidão: identificar tabelas, modelagem BCNF, Formas Normais nem pensadas, filtros, índices e cardinalidade.

## Nota de risco (tecnologia)
- Usar SOLID, Padrões de Projeto, DDD, Testes Unitários, Métricas, perfomance forte é inegociável.

## Segurança
- Spring Security

