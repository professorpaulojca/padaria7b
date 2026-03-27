# Continuação — Protótipos do Sistema Padaria

**Última sessão:** 26/03/2026  
**Status:** 4 de 13 protótipos concluídos

---

## ✅ Concluídos

| # | Arquivo | Módulos | Descrição |
|---|---------|---------|-----------|
| 1 | `login-dashboard.html` | M00 + M12 | Login (senha + PIN 6 perfis), Dashboard com KPIs, gráficos, alertas |
| 2 | `atendimento.html` | M06 | Novo Pedido (modalidade → mesa/delivery → produtos → carrinho), Pedidos, Cozinha (kanban), Mesas |
| 3 | `caixa.html` | M07 | Abertura/Fechamento caixa, Pagamentos (6 formas + misto), Sangria/Suprimento, Conferência Cega |
| 4 | `financeiro.html` | M08 | Contas Pagar/Receber, Fiado c/ detalhe, Boletos Bradesco (CNAB 400, remessa/retorno), DRE, Fluxo de Caixa |

---

## ⬜ Pendentes (9 protótipos)

| # | Arquivo | Módulos | O que criar |
|---|---------|---------|-------------|
| 5 | `cadastros.html` | M01 + M02 | CRUD: Produtos, Ingredientes, Categorias, Unidades, Clientes, Empregados, Fornecedores, Formas de Pagamento |
| 6 | `estoque.html` | M03 | Dashboard estoque, Gestão de Lotes, Alertas de Validade (PVPS), Baixa automática, Inventário |
| 7 | `compras.html` | M04 | Pedidos de compra, Recebimento/conferência, Histórico de preços, Comparativo fornecedores |
| 8 | `producao.html` | M05 | Fichas técnicas (receitas), Ordens de produção, Cálculo de custo por produto |
| 9 | `pessoal.html` | M09 | Registro de ponto, Escalas de trabalho, Vales/adiantamentos, Ocorrências |
| 10 | `higiene.html` | M10 | Checklists de limpeza, Controle dedetização, APPCC, Registro de temperatura |
| 11 | `manutencao.html` | M11 | Ficha de equipamentos, Manutenção preventiva e corretiva, Histórico |
| 12 | `relatorios.html` | M12 | 13 tipos de relatório com filtros, Exportação PDF/CSV |
| 13 | `configuracoes.html` | M13 | Parâmetros do sistema, Config Bradesco (agência/conta/carteira), Dados da empresa, Seed data |

---

## Padrão a seguir

Todos os protótipos devem:
- Ser **HTML self-contained** (CSS + JS embutidos, sem dependências externas)
- Usar as **mesmas CSS variables** (--primary:#E8722A, etc.)
- Ter **sidebar 72px** com links para os demais módulos
- Ter **header sticky** com título, subtítulo e badge
- Usar **tabs-bar** para subpáginas internas
- Incluir **mock data** realista em JavaScript
- Ter **modals animados** para formulários/detalhes
- Ter **toast notifications** para feedback
- Ser **responsivo** (breakpoints: 900px e 600px)
- Seguir UX estilo **McDonald's**: botões grandes, cards visuais, fluxos passo-a-passo

## Specs de referência
Todas as specs estão prontas em `specs/`:
- `000-seguranca-acesso/spec.md` a `014-mobile-acessibilidade/spec.md`
- Planejamento mestre: `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`
