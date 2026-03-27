# SPEC-001 — Cadastros Gerais

> **Módulo**: M01 – Cadastros Gerais  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: ALTA — Base de dados mestre para todo o sistema  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Manter os cadastros mestres que sustentam todo o sistema: produtos vendidos, ingredientes/insumos, categorias, mesas, unidades de medida, formas de pagamento e materiais de apoio. Sem este módulo, nenhum outro funciona.

### Evidências

- **RF01**: "Cadastrar produtos vendidos pela padaria"
- **RF02**: "Cadastrar ingredientes e insumos da produção"
- **OE-01**: "Controlar estoque de ingredientes, produtos prontos, materiais de apoio e itens de limpeza."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-CAD-01** | Cadastrar produtos vendidos: descrição, categoria, preço de venda, unidade de venda, foto opcional, situação (ativo/inativo), código de barras opcional | RF01 |
| **RF-CAD-02** | Cadastrar ingredientes/insumos: descrição, categoria, unidade de medida, estoque mínimo, perecível (S/N), dias de validade padrão | RF02 |
| **RF-CAD-03** | Cadastrar categorias hierárquicas: grupo (ex.: "Pães") → sub-grupo (ex.: "Pães Doces") | Organização |
| **RF-CAD-04** | Cadastrar mesas: número, capacidade, localização (salão/calçada/interna), situação (disponível/ocupada/reservada/inativa) | RF08 |
| **RF-CAD-05** | Cadastrar unidades de medida: kg, g, L, mL, unidade, pacote, caixa, dúzia | Padronização |
| **RF-CAD-06** | Cadastrar formas de pagamento aceitas: dinheiro, cartão débito, cartão crédito, PIX, fiado, vale-refeição | RF11 |
| **RF-CAD-07** | Manter histórico de alteração de preço: data, preço anterior, preço novo, quem alterou | Auditoria financeira |
| **RF-CAD-08** | Cadastrar materiais de apoio e limpeza: sacolas, embalagens, detergente, álcool, papel toalha (classe separada de insumo) | EST-06, HIG-06 |
| **RF-CAD-09** | Importação em lote de produtos via planilha CSV (facilitar carga inicial) | Praticidade |
| **RF-CAD-10** | Busca inteligente de produtos: por nome parcial, categoria, código de barras, situação | ROP-02, ATD-01 |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Dono / Gerente** | Acesso total: criar, editar, inativar qualquer cadastro. Alterar preços. |
| **Administrativo** | Acesso total: criar, editar, inativar (mesmos privilégios operacionais). |
| **Caixa / Atendente** | Somente leitura: consultar produtos, preços, disponibilidade. |
| **Chapeiro** | Sem acesso direto. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Produto (vendido ao cliente)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `descricao` | texto | Sim | Nome do produto (ex.: "Pão Francês", "Café com Leite") |
| `categoria_id` | referência | Sim | Categoria do produto |
| `preco_venda` | monetário | Sim | Preço atual de venda |
| `unidade_venda_id` | referência | Sim | Unidade (un, kg, fatia, copo) |
| `codigo_barras` | texto | Não | Código de barras (EAN) |
| `foto` | binário/path | Não | Imagem do produto |
| `situacao` | enum | Sim | `ATIVO`, `INATIVO` |
| `disponivel` | booleano | Sim | Se está disponível para venda no momento (pode estar ativo mas indisponível temporariamente) |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.2 Ingrediente / Insumo

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `descricao` | texto | Sim | Nome do insumo (ex.: "Trigo Especial 50kg") |
| `categoria_id` | referência | Sim | Categoria do insumo |
| `unidade_medida_id` | referência | Sim | Unidade padrão (kg, L, un) |
| `estoque_minimo` | numérico | Sim | Quantidade mínima para alerta |
| `perecivel` | booleano | Sim | Se é perecível (ativa controle de validade) |
| `dias_validade_padrao` | inteiro | Não | Dias de validade típicos após recebimento (para pré-preencher) |
| `tipo` | enum | Sim | `INSUMO_PRODUCAO`, `MATERIAL_APOIO`, `MATERIAL_LIMPEZA` |
| `situacao` | enum | Sim | `ATIVO`, `INATIVO` |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.3 Categoria

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome da categoria |
| `tipo` | enum | Sim | `PRODUTO`, `INSUMO` — para separar árvores |
| `categoria_pai_id` | referência | Não | Referência ao grupo pai (hierarquia simples, 2 níveis) |
| `ordem` | inteiro | Não | Ordem de exibição na interface |
| `situacao` | enum | Sim | `ATIVA`, `INATIVA` |

### 4.4 Mesa

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `numero` | inteiro | Sim | Número visível da mesa (UNIQUE) |
| `capacidade` | inteiro | Não | Quantidade de lugares |
| `localizacao` | enum | Sim | `SALAO`, `CALCADA`, `INTERNA`, `OUTRO` |
| `situacao` | enum | Sim | `DISPONIVEL`, `OCUPADA`, `RESERVADA`, `INATIVA` |

### 4.5 Unidade de Medida

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome completo (ex.: "Quilograma") |
| `sigla` | texto | Sim | Abreviação (ex.: "kg") — UNIQUE |
| `tipo` | enum | Sim | `PESO`, `VOLUME`, `UNIDADE`, `EMBALAGEM` |

### 4.6 Forma de Pagamento

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome (ex.: "Dinheiro", "PIX", "Cartão Crédito") |
| `tipo` | enum | Sim | `DINHEIRO`, `CARTAO_DEBITO`, `CARTAO_CREDITO`, `PIX`, `FIADO`, `VALE_REFEICAO`, `OUTRO` |
| `ativo` | booleano | Sim | Se está habilitada |
| `exige_identificacao` | booleano | Sim | Se exige identificar cliente (ex.: fiado = sim) |

### 4.7 Histórico de Preço

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `produto_id` | referência | Sim | Produto afetado |
| `preco_anterior` | monetário | Sim | Preço antes da alteração |
| `preco_novo` | monetário | Sim | Preço depois da alteração |
| `alterado_por` | referência | Sim | Usuário que alterou |
| `data_alteracao` | timestamp | Sim | Quando foi alterado |

---

## 5. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-CAD-01** | Produto não pode ser excluído se já foi usado em algum pedido. Deve ser inativado. |
| **RN-CAD-02** | Insumo não pode ser excluído se possui movimentação de estoque ou está em ficha técnica. Deve ser inativado. |
| **RN-CAD-03** | Alteração de preço de produto gera automaticamente registro no histórico de preços (RF-CAD-07). |
| **RN-CAD-04** | Produto inativo não aparece para seleção em novos pedidos, mas permanece visível em pedidos já registrados. |
| **RN-CAD-05** | Categoria com produtos/insumos vinculados não pode ser excluída. Deve ser inativada. |
| **RN-CAD-06** | Mesa com comanda aberta não pode ser inativada (fechar comanda primeiro). |
| **RN-CAD-07** | Código de barras, se informado, deve ser único entre produtos ativos. |
| **RN-CAD-08** | Toda alteração de cadastro gera log de auditoria (M00). |
| **RN-CAD-09** | Importação CSV valida campos obrigatórios e rejeita linhas com erro, informando quais falharam. |
| **RN-CAD-10** | Número da mesa é único e sequencial (ex.: 1, 2, 3...), mas pode haver lacunas. |
| **RN-CAD-11** | Unidades de medida padrão são pré-cadastradas (seed data) e não podem ser excluídas. |
| **RN-CAD-12** | Formas de pagamento padrão são pré-cadastradas e não podem ser excluídas, apenas desativadas. |

---

## 6. Fluxos Principais

### 6.1 Cadastrar Novo Produto

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Novo Produto" ----->|
   |-- Preenche: descrição, ------>|
   |   categoria, preço, unidade,  |
   |   código barras (opcional),   |
   |   foto (opcional)             |
   |-- Clica "Salvar" ------------>|-- Valida campos obrigatórios
   |                               |-- Valida unicidade código barras
   |                               |-- Salva com situacao=ATIVO, disponivel=true
   |                               |-- Registra auditoria
   |<-- "Produto cadastrado" ------|
```

### 6.2 Alterar Preço

```
Dono/Admin                       Sistema
   |                               |
   |-- Abre produto existente ---->|
   |-- Altera preço de venda ----->|
   |-- Clica "Salvar" ------------>|-- Grava registro em historico_preco
   |                               |     (preco_anterior, preco_novo, quem, quando)
   |                               |-- Atualiza preco_venda no produto
   |                               |-- Registra auditoria
   |<-- "Preço atualizado" --------|
```

### 6.3 Importação CSV

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Importar CSV" ----->|
   |-- Faz upload do arquivo ----->|-- Valida formato (colunas esperadas)
   |                               |-- Processa linha a linha:
   |                               |     ✓ Linhas válidas → cadastra
   |                               |     ✗ Linhas inválidas → registra erro
   |                               |-- Gera relatório de importação
   |<-- "X produtos importados, ---|
   |     Y erros encontrados"      |
   |-- (Visualiza detalhes erros)->|
```

---

## 7. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-CAD-01 | Lista de produtos: tabela com busca, filtro por categoria e situação, paginação |
| UI-CAD-02 | Formulário de produto: campos organizados, preview de foto, seleção de categoria em dropdown hierárquico |
| UI-CAD-03 | Busca rápida de produto: campo de busca com resultados instantâneos (autocomplete) |
| UI-CAD-04 | Mapa de mesas: visualização gráfica simplificada das mesas com status por cor |
| UI-CAD-05 | Indicador visual de produtos indisponíveis: botão toggle "disponível/indisponível" rápido |
| UI-CAD-06 | Histórico de preço acessível ao clicar no produto: gráfico simples ou lista cronológica |

---

## 8. Seed Data

| Entidade | Dados Iniciais |
|---|---|
| Unidades de Medida | kg, g, L, mL, unidade (un), pacote (pct), caixa (cx), dúzia (dz), fatia, copo |
| Formas de Pagamento | Dinheiro, Cartão Débito, Cartão Crédito, PIX, Fiado, Vale-Refeição |
| Categorias Produto | Pães, Doces e Bolos, Salgados, Bebidas, Frios e Laticínios, Refeições, Outros |
| Categorias Insumo | Farinhas, Açúcares e Adoçantes, Laticínios, Óleos e Gorduras, Fermentos, Frios, Embalagens, Limpeza, Outros |
