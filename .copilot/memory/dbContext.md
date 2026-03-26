# DB Context (PostgreSQL)
- Flyway para migrações (Java Spring)
- Entidades e campos em minúsculo

## Schemas
- A definir na SPEC/PLAN. Candidatos: schema único `padaria` ou separação por módulo.

## Tabelas-chave (previstas, baseadas nos RFs)
- produtos, ingredientes, categorias
- estoque_movimentacao, fornecedores, compras
- pedidos, pedido_itens, comandas, mesas
- pagamentos, caixa_movimentacao
- clientes, fiado, fiado_movimentacao
- funcionarios, vales, ponto_registro
- manutencao_tarefas, higiene_checklist

> **NOTA**: Nomes e estruturas são preliminares. Modelagem definitiva na SPEC.

## Packages/Procedures críticas
- A definir

## Transações e commits
- Transações explícitas obrigatórias (constitution.md)
