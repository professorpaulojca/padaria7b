# Checklist de Homologação dos Protótipos

Este roteiro serve para validar visualmente e funcionalmente os protótipos HTML do sistema da padaria.

## Regras gerais para todos os arquivos

Validar em cada HTML:

- abre sem erro visual logo no carregamento
- sidebar aparece e permite navegar para outros módulos
- header sticky permanece correto ao rolar a página
- tabs internas alternam conteúdo sem quebrar layout
- botões principais respondem com modal, toast ou troca de estado
- tabelas, cards e KPIs aparecem com dados mockados
- página funciona em desktop e também em largura reduzida
- textos não ficam cortados ou sobrepostos
- nenhum link local leva para arquivo inexistente

## Sequência sugerida

1. `index.html`
2. `login-dashboard.html`
3. `atendimento.html`
4. `caixa.html`
5. `financeiro.html`
6. `cadastros.html`
7. `estoque.html`
8. `compras.html`
9. `producao.html`
10. `pessoal.html`
11. `higiene.html`
12. `manutencao.html`
13. `relatorios.html`
14. `configuracoes.html`

## Checklist por arquivo

### `index.html`

- portal abre com os 13 módulos visíveis
- busca filtra cards por nome ou tema
- filtro por grupo altera a grade corretamente
- botões de abrir levam para os HTMLs corretos
- fluxo recomendado aparece completo

### `login-dashboard.html`

- fluxo de login inicia sem travamento visual
- perfis e PIN/entrada simulada ficam claros
- dashboard carrega KPIs, gráficos e alertas
- atalhos ou navegações principais respondem

### `atendimento.html`

- seleção de modalidade funciona
- jornada de pedido mostra produtos e carrinho
- mudança entre mesas, balcão e delivery faz sentido
- telas de pedidos/cozinha não quebram ao trocar tabs

### `caixa.html`

- abertura e fechamento de caixa estão acessíveis
- botões de pagamento e operações de caixa respondem
- sangria, suprimento e conferência aparecem com feedback
- indicadores do caixa permanecem coerentes

### `financeiro.html`

- contas a pagar e receber aparecem corretamente
- seção de fiado abre detalhes sem quebrar layout
- parte de boletos Bradesco e CNAB está legível
- DRE e fluxo de caixa carregam mock data

### `cadastros.html`

- tabs de produtos, clientes e fornecedores trocam normalmente
- busca e filtros atualizam as listas
- modais de cadastro abrem e fecham corretamente
- cards e tabelas mantêm consistência visual

### `estoque.html`

- dashboard de estoque mostra alertas e saldos
- listas de lotes e validade aparecem preenchidas
- inventário, perdas e movimentações mudam por tabs
- ações de ajuste ou baixa geram feedback visual

### `compras.html`

- pedidos de compra aparecem com status coerentes
- recebimento/conferência está navegável
- comparativo de fornecedores está legível
- histórico de preços carrega corretamente

### `producao.html`

- fichas técnicas e ordens aparecem corretamente
- sugestão de produção fica visível e compreensível
- custos e margens são exibidos sem quebra visual
- ações principais mostram modal ou toast

### `pessoal.html`

- ponto, escala e ocorrências trocam por tabs
- resumos mensais da equipe estão visíveis
- vales e alertas ficam claros no layout
- cards e tabelas não estouram em telas menores

### `higiene.html`

- dashboard sanitário carrega alertas e indicadores
- checklists e temperaturas mudam por tabs
- dedetização e inspeções abrem ações corretamente
- feedbacks de conformidade ficam visíveis

### `manutencao.html`

- lista de equipamentos carrega corretamente
- preventivas e corretivas exibem status coerentes
- histórico e custos podem ser acessados sem erro
- modais principais funcionam até o toast final

### `relatorios.html`

- dashboard executivo abre com KPIs e visões resumidas
- catálogo dos relatórios responde à busca
- tabs de vendas, operação, financeiro e auditoria funcionam
- exportação PDF/CSV abre modal e mostra feedback

### `configuracoes.html`

- dados da empresa estão visíveis e editáveis no protótipo
- tabs de operação, segurança, Bradesco, backup e seed data funcionam
- modal de confirmação de segurança abre corretamente
- horários, parâmetros e seed data ficam legíveis

## Registro de validação

Use este formato durante a revisão:

- `OK`: aprovado sem ajuste
- `AJUSTAR`: funciona, mas precisa refinamento visual ou textual
- `ERRO`: comportamento quebrado ou navegação inválida

Modelo rápido:

```text
index.html - OK
login-dashboard.html - AJUSTAR - texto pequeno no mobile
caixa.html - ERRO - botão X não fecha modal Y
```
