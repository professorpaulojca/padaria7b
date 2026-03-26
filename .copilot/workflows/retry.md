# Workflow: Retry (quando travar)

Use quando:
- não consegue reproduzir
- hipóteses se contradizem
- o problema parece “fantasma”

Passos:
1. Reduzir o escopo (um endpoint/uma tela/uma procedure).
2. Criar caso mínimo reproduzível (request sample, dataset pequeno).
3. Adicionar logs/telemetria temporária (remover ao final se não fizer sentido manter).
4. Voltar ao plano com nova evidência.
