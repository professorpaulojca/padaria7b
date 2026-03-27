# SPEC-012 — Relatórios e Dashboards

> **Módulo**: M12 – Relatórios e Dashboards  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: BAIXA (implementação final, mas essencial para gestão)  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Consolidar informações de todos os módulos em dashboards visuais e relatórios exportáveis, fornecendo ao dono e gerente uma visão clara do desempenho da padaria para tomada de decisão.

### Evidências

- **REL-01 a REL-15**: Requisitos de relatórios mapeados no planejamento expandido.
- **RNF-09**: "O sistema deve registrar log de toda operação financeira e de estoque."
- **ROP**: "Visualizar indicadores diários de vendas, estoque e caixa."
- **Quadro de Problemas**: "Sem visibilidade de números. Dono gerencia no feeling."

---

## 2. Requisitos Funcionais

| Código | Descrição | Fonte de Dados |
|---|---|---|
| **RF-REL-01** | Dashboard principal com KPIs do dia: vendas, ticket médio, caixa atual, pedidos, itens baixo estoque | M06, M07, M03 |
| **RF-REL-02** | Relatório de vendas por período: total, por forma de pagamento, por produto, por modalidade (balcão/mesa/viagem/delivery) | M06, M07 |
| **RF-REL-03** | Relatório de produtos mais vendidos (ranking) e menos vendidos (potenciais cortes) | M06 |
| **RF-REL-04** | Relatório de estoque atual: saldo, valor em estoque, itens vencidos/próximos do vencimento | M03 |
| **RF-REL-05** | Relatório de movimentação de estoque por período: entradas, saídas, perdas (desperdício) | M03 |
| **RF-REL-06** | Relatório financeiro: contas a pagar, contas a receber, saldo (DRE simplificado) | M08 |
| **RF-REL-07** | Relatório de fiado: clientes com saldo devedor, aging (7/15/30+ dias), total em aberto | M08, M02 |
| **RF-REL-08** | Relatório de caixa: histórico de aberturas/fechamentos, diferenças, sangrias | M07 |
| **RF-REL-09** | Relatório de pessoal: resumo mensal (horas, atrasos, faltas, vales) | M09 |
| **RF-REL-10** | Relatório de higiene e conformidade sanitária: % checklists conformes, dedetizações, temperaturas | M10 |
| **RF-REL-11** | Relatório de manutenção: custos, preventivas executadas/pendentes, corretivas | M11 |
| **RF-REL-12** | Relatório de compras: volume e valor por fornecedor, comparativo de preços | M04 |
| **RF-REL-13** | Relatório de produção: receitas produzidas, consumo de insumos, custo de produção | M05 |
| **RF-REL-14** | Exportação de relatórios em PDF e CSV | Todos |
| **RF-REL-15** | Filtros configuráveis: período, categoria, funcionário, fornecedor, equipamento etc. | Todos |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono** | Acesso total a todos os relatórios e dashboards. |
| **Gerente** | Acesso total a todos os relatórios e dashboards. |
| **Administrativo** | Acesso a relatórios de estoque, compras, manutenção, higiene. Sem acesso ao financeiro detalhado (conforme permissão M00). |
| **Caixa** | Acesso ao relatório do próprio caixa (abertura/fechamento do dia). |
| **Demais** | Sem acesso a relatórios. |

---

## 4. Dashboard Principal (RF-REL-01)

### 4.1 Estrutura do Dashboard

O dashboard é a tela inicial para Dono/Gerente após login. Organizado em cards/widgets:

| Widget | Dados | Atualização |
|---|---|---|
| **Vendas Hoje** | Total R$, nº pedidos, ticket médio | Tempo real |
| **Comparativo** | Vendas hoje vs. mesmo dia semana passada (↑↓ %) | Diário |
| **Caixa Atual** | Saldo em caixa (se aberto), última sangria | Tempo real |
| **Pedidos** | Abertos, em preparo, prontos (contadores) | Tempo real |
| **Estoque Crítico** | Itens abaixo do mínimo (count + lista resumida) | Atualizado por movimento |
| **Validades** | Lotes vencendo em 1/3/7 dias (count) | Job diário |
| **Fiado** | Total em aberto, inadimplentes >30 dias (count) | Atualizado por lançamento |
| **Alertas** | Reunião de alertas: preventivas, checklists, dedetização | Consolidado |

### 4.2 Gráficos do Dashboard

| Gráfico | Tipo | Dados |
|---|---|---|
| Vendas 7 dias | Linha | Total de vendas por dia nos últimos 7 dias |
| Vendas por modalidade | Pizza/Donut | Balcão × Mesa × Viagem × Delivery (período selecionável) |
| Top 5 produtos | Barras horizontal | Produtos mais vendidos do dia |
| Forma de pagamento | Pizza/Donut | Distribuição: Dinheiro, Débito, Crédito, PIX, Fiado |

---

## 5. Detalhamento dos Relatórios

### 5.1 Vendas (RF-REL-02 e RF-REL-03)

**Filtros**: Período (de/até), modalidade, forma de pagamento, categoria de produto.

| Seção | Conteúdo |
|---|---|
| Resumo | Total bruto, descontos, total líquido, nº pedidos, ticket médio |
| Por forma de pagamento | Tabela: forma, quantidade, total R$, % do total |
| Por modalidade | Tabela: balcão, mesa, viagem, delivery — qtd pedidos, total R$ |
| Por produto (ranking) | Tabela: produto, qtd vendida, receita, % do total — ordenado por receita desc |
| Por dia | Tabela/gráfico: vendas dia a dia no período |
| Por hora | Gráfico de barras: vendas por hora do dia (identifica picos) |

### 5.2 Estoque (RF-REL-04 e RF-REL-05)

**Filtros**: Data, categoria, situação (ativo/inativo), classificação ABC.

| Seção | Conteúdo |
|---|---|
| Posição atual | Tabela: item, saldo, unidade, valor unitário, valor total, estoque mínimo, status |
| Vencimentos | Tabela: lote, item, validade, dias restantes (vermelho/amarelo/verde) |
| Movimentação | Tabela: data, item, tipo (entrada/saída/ajuste/perda), quantidade, origem |
| Perdas | Total de perdas (R$) por período, por motivo (vencido, avariado, uso produção) |
| Classificação ABC | Tabela com classificação e valor em estoque por faixa |

### 5.3 Financeiro (RF-REL-06)

**Filtros**: Período, categoria, situação (pago/pendente/vencido).

| Seção | Conteúdo |
|---|---|
| DRE Simplificado | Receitas - Despesas = Resultado (por categoria) |
| Contas a pagar | Tabela: fornecedor/descrição, valor, vencimento, situação |
| Contas a receber | Tabela: cliente, valor, origem, vencimento, situação |
| Fluxo de caixa | Gráfico: entradas vs. saídas por semana/mês |
| Despesas por categoria | Pizza: Estoque, Pessoal, Manutenção, Limpeza, Fixas, Variáveis |

### 5.4 Fiado (RF-REL-07)

**Filtros**: Período, cliente, situação (em dia/atrasado).

| Seção | Conteúdo |
|---|---|
| Resumo | Total em aberto, total recebido no período, taxa de inadimplência |
| Aging | Faixas: até 7 dias, 8-15 dias, 16-30 dias, >30 dias — quantidade e valor |
| Por cliente | Tabela: cliente, total devido, fiados mais antigos, último pagamento |
| Detalhamento | Drill-down por cliente: cada fiado, valor, data, pagamentos parciais |

### 5.5 Caixa (RF-REL-08)

**Filtros**: Período, operador.

| Seção | Conteúdo |
|---|---|
| Aberturas/Fechamentos | Tabela: data, operador, abertura, valor inicial, fechamento, valor final, diferença |
| Sangrias | Tabela: data, valor, motivo, quem autorizou |
| Diferenças | Total de diferenças positivas e negativas no período |
| Movimentação | Timeline: todos os movimentos (venda, sangria, reforço, vale) |

### 5.6 Pessoal (RF-REL-09)

**Filtros**: Mês, funcionário.

| Seção | Conteúdo |
|---|---|
| Resumo por funcionário | Tabela: nome, dias trabalhados, horas totais, atrasos, faltas, vales R$ |
| Custo de pessoal | Total: salários + vales do período |
| Ocorrências | Lista: funcionário, tipo, data, descrição |

### 5.7 Higiene (RF-REL-10)

**Filtros**: Período, área.

| Seção | Conteúdo |
|---|---|
| Conformidade | % de checklists conformes no período (geral e por área) |
| Detalhamento | Tabela: checklist, data, executor, status, itens não conformes |
| Temperaturas | Gráfico por equipamento: temperaturas registradas (com faixa aceitável) |
| Pragas | Status: última dedetização, validade, alerta |
| Inspeções | Tabela: data, resultado, não-conformidades, resolução |

### 5.8 Manutenção (RF-REL-11)

**Filtros**: Período, equipamento.

| Seção | Conteúdo |
|---|---|
| Custos | Total de manutenção no período, preventiva vs. corretiva |
| Por equipamento | Tabela: equipamento, nº preventivas, nº corretivas, custo total, tempo parado |
| Pendências | Lista: preventivas atrasadas ou próximas do vencimento |
| Histórico | Timeline de manutenções por equipamento |

### 5.9 Compras (RF-REL-12)

**Filtros**: Período, fornecedor, insumo.

| Seção | Conteúdo |
|---|---|
| Volume | Total de compras no período (R$), nº pedidos |
| Por fornecedor | Tabela: fornecedor, nº pedidos, total R$, % do total |
| Comparativo de preços | Tabela: insumo, fornecedor A preço, fornecedor B preço, variação |
| Evolução de preços | Gráfico: preço de insumos-chave ao longo do tempo |

### 5.10 Produção (RF-REL-13)

**Filtros**: Período, receita/produto.

| Seção | Conteúdo |
|---|---|
| Produção total | Nº de ordens, qtd produzida por produto |
| Consumo de insumos | Tabela: insumo, qtd consumida pela produção, custo |
| Custo de produção | Por produto: custo unitário, total, margem estimada (venda - custo) |
| Perdas de produção | Qtd e valor de perdas durante produção |

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-REL-01** | Acesso a relatórios controlado por permissão do módulo fonte (M00). Ex.: sem permissão em M08, sem relatório financeiro. |
| **RN-REL-02** | Dashboard principal carrega dados em tempo real para widgets de vendas, caixa e pedidos. |
| **RN-REL-03** | Relatórios paginados: máximo de 500 registros por página para performance. |
| **RN-REL-04** | Exportação PDF: formato padronizado com cabeçalho (nome padaria + endereço, de M13), filtros aplicados, data/hora de geração. |
| **RN-REL-05** | Exportação CSV: sem formatação, separador ponto-e-vírgula (padrão BR), encoding UTF-8 com BOM. |
| **RN-REL-06** | Dados de relatório são sempre read-only. Nenhuma alteração de dados é feita neste módulo. |
| **RN-REL-07** | Para comparativos (dia anterior, semana anterior), o sistema busca dados históricos já persistidos. |
| **RN-REL-08** | Filtro padrão de período: "Hoje" para dashboard, "Mês atual" para relatórios. |
| **RN-REL-09** | Todo acesso a relatório gera log de auditoria (M00) para rastreabilidade. |

---

## 7. Fluxo Principal

### 7.1 Acesso ao Dashboard

```
Dono/Gerente                     Sistema
   |                               |
   |-- Faz login ----------------->|
   |                               |-- Carrega dashboard principal
   |                               |-- Widgets buscam dados em paralelo
   |<-- Dashboard renderizado -----|
   |                               |
   |-- Clica widget "Vendas" ----->|-- Abre relatório de vendas detalhado
   |-- Ajusta filtros (período) -->|-- Recalcula
   |-- Exporta PDF --------------->|-- Gera PDF com cabeçalho + filtros + dados
   |<-- Download do arquivo -------|
```

### 7.2 Geração de Relatório

```
Usuário                          Sistema
   |                               |
   |-- Menu "Relatórios" --------->|-- Lista relatórios conforme permissão
   |-- Seleciona "Vendas" -------->|-- Exibe tela com filtros
   |-- Define filtros: ----------->|
   |   Período: 01/03 a 31/03     |
   |   Modalidade: Todas           |
   |-- Clica "Gerar" ------------->|-- Consulta dados
   |                               |-- Monta seções do relatório
   |<-- Exibe relatório na tela ---|
   |                               |
   |-- [Exportar PDF] ------------>|-- Gera PDF  
   |-- [Exportar CSV] ------------>|-- Gera CSV  
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-REL-01 | Dashboard: layout responsivo com grid de cards. Em mobile: cards empilhados com scroll vertical. |
| UI-REL-02 | Gráficos: biblioteca de charts (ex.: Chart.js ou Recharts) com cores do design system (M14). |
| UI-REL-03 | Filtros: barra colapsável no topo de cada relatório. Em mobile: drawer lateral. |
| UI-REL-04 | Tabelas: paginação, ordenação por coluna, busca textual. |
| UI-REL-05 | PDF: cabeçalho com logo/nome da padaria (M13), rodapé com página e data/hora. |
| UI-REL-06 | Loading: skeleton screens nos cards e tabelas durante carregamento. |
| UI-REL-07 | Drill-down: clique em dado resumido (ex.: total de vendas de um produto) → detalhe. |
| UI-REL-08 | Impressão direta: botão "Imprimir" que aciona `window.print()` com CSS otimizado. |

---

## 9. KPIs Sugeridos (Dashboard)

| KPI | Cálculo | Meta Sugerida (configurável) |
|---|---|---|
| Ticket médio | Receita total / nº pedidos | Depende do porte |
| Perda de estoque (%) | Valor perdido / valor total movimentado × 100 | < 3% |
| Inadimplência fiado (%) | Fiados > 30 dias / total fiado × 100 | < 10% |
| Diferença de caixa (%) | Soma diferenças / recebido total × 100 | < 1% |
| Conformidade sanitária (%) | Checklists conformes / total checklists × 100 | > 95% |
| Manutenções em dia (%) | Preventivas executadas no prazo / total programadas × 100 | > 90% |
| Custo de produção vs. venda | Custo MP / preço de venda × 100 | < 40% |
