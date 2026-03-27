# SPEC-004 — Compras e Fornecedores

> **Módulo**: M04 – Compras e Fornecedores  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA-ALTA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Organizar o processo de compra desde a identificação da necessidade até o recebimento e conferência, com histórico de preços, comparação de fornecedores e integração automática com estoque (M03) e contas a pagar (M08).

### Evidências

- **RF06**: "Cadastrar fornecedores e registrar compras de mercadorias e materiais."
- **EST-03**: "Níveis mínimos de estoque para itens importantes. Não depender da memória."
- **EST-04**: "Dono ou funcionário de confiança confirma entrada no recebimento."
- **Quadro de Problemas**: "Falta trigo, sobra sal; insumos comprados sem visão confiável do saldo real."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-CMP-01** | Gerar lista de compra automática a partir de itens abaixo do mínimo ou sugestão do M03 | EST-03 |
| **RF-CMP-02** | Criar pedido de compra: fornecedor, itens, quantidades, preço negociado, prazo de entrega, condição de pagamento | RF06 |
| **RF-CMP-03** | Registrar recebimento: conferência item a item, quantidade recebida vs. pedida, datas de validade dos lotes | EST-04 |
| **RF-CMP-04** | Registrar divergências no recebimento: faltante, quantidade diferente, danificado, validade curta | EST-04 |
| **RF-CMP-05** | Histórico de preços por item/fornecedor: evolução ao longo do tempo | Gestão de custos |
| **RF-CMP-06** | Comparação de fornecedores: mesmo item, preços diferentes, prazos, avaliações | Gestão de custos |
| **RF-CMP-07** | Gerar conta a pagar automaticamente ao confirmar recebimento (integração com M08) | Financeiro |
| **RF-CMP-08** | Relatório de compras por período, fornecedor, categoria de insumo | Controle |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono / Gerente** | Acesso total. Aprova pedidos de compra. Define fornecedores preferenciais. |
| **Administrativo** | Cria pedidos de compra, realiza recebimento, confere mercadorias. |
| **Demais** | Sem acesso. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Pedido de Compra

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `numero` | sequencial | Sim | Número do pedido de compra |
| `fornecedor_id` | referência | Sim | Fornecedor (FK → M02) |
| `status` | enum | Sim | `RASCUNHO`, `ENVIADO`, `RECEBIDO_PARCIAL`, `RECEBIDO_TOTAL`, `CANCELADO` |
| `data_pedido` | data | Sim | Data de emissão |
| `data_previsao_entrega` | data | Não | Previsão de entrega |
| `data_recebimento` | data | Não | Data efetiva de recebimento |
| `condicao_pagamento` | texto | Não | Ex.: "À vista", "30 dias" |
| `valor_total` | monetário | Sim | Soma dos itens |
| `observacao` | texto | Não | Observações |
| `criado_por` | referência | Sim | Quem criou |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.2 Item do Pedido de Compra

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `pedido_compra_id` | referência | Sim | Pedido pai |
| `insumo_id` | referência | Sim | Insumo solicitado (FK → M01) |
| `quantidade_pedida` | numérico | Sim | Quantidade solicitada |
| `quantidade_recebida` | numérico | Não | Quantidade efetivamente recebida |
| `preco_unitario` | monetário | Sim | Preço negociado |
| `data_validade` | data | Não | Preenchida no recebimento |
| `lote` | texto | Não | Código do lote (preenchido no recebimento) |
| `divergencia` | texto | Não | Descrição da divergência (se houver) |

### 4.3 Histórico de Preço de Compra

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `insumo_id` | referência | Sim | Insumo |
| `fornecedor_id` | referência | Sim | Fornecedor |
| `preco_unitario` | monetário | Sim | Preço praticado |
| `data` | data | Sim | Data da compra |
| `pedido_compra_id` | referência | Sim | Pedido de compra de origem |

---

## 5. Ciclo de Vida do Pedido de Compra

```
RASCUNHO → ENVIADO → RECEBIDO_PARCIAL → RECEBIDO_TOTAL
                ↓                              ↓
            CANCELADO                      CANCELADO
```

| Estado | Descrição |
|---|---|
| `RASCUNHO` | Pedido em elaboração. Pode ser editado livremente. |
| `ENVIADO` | Pedido confirmado e enviado ao fornecedor. Itens travados. |
| `RECEBIDO_PARCIAL` | Parte dos itens foi recebida. Restante pendente. |
| `RECEBIDO_TOTAL` | Todos os itens recebidos (com ou sem divergências). Estado terminal. |
| `CANCELADO` | Pedido cancelado. Estado terminal. |

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-CMP-01** | Ao confirmar recebimento, o sistema cria automaticamente movimentos de ENTRADA no estoque (M03) com lote e validade. |
| **RN-CMP-02** | Ao confirmar recebimento total, o sistema gera conta a pagar em M08 com valor total e vencimento baseado na condição de pagamento. |
| **RN-CMP-03** | Todo recebimento registra o preço praticado no histórico de preço de compra para análise futura. |
| **RN-CMP-04** | Divergências de recebimento devem ser registradas obrigatoriamente (quantidade diferente, item danificado, validade curta). |
| **RN-CMP-05** | Lista de compra automática: prioriza fornecedor preferencial (melhor avaliação ou menor preço médio). |
| **RN-CMP-06** | Pedido de compra só pode ser editado no estado RASCUNHO. |
| **RN-CMP-07** | Cancelamento de pedido ENVIADO exige motivo obrigatório. |
| **RN-CMP-08** | Todo registro gera log de auditoria (M00). |

---

## 7. Fluxos Principais

### 7.1 Gerar Pedido de Compra a partir de Sugestão

```
Administrativo                   Sistema
   |                               |
   |-- Acessa "Sugestão Compra" -->|-- Lista itens abaixo do mín (M03)
   |   (gerado pelo M03)          |   + informações de fornecedor
   |-- Seleciona itens e --------->|
   |   quantidades desejadas       |
   |-- Seleciona fornecedor ------>|-- Pré-preenche preços do último pedido
   |-- Ajusta preços/condição ---->|
   |-- Salva como RASCUNHO ------->|-- Cria pedido de compra
   |-- Confirma envio ------------>|-- Status → ENVIADO
   |                               |-- Registra auditoria
   |<-- "Pedido #X enviado" -------|
```

### 7.2 Recebimento de Compra

```
Administrativo                   Sistema
   |                               |
   |-- Acessa pedido ENVIADO ----->|-- Exibe itens do pedido
   |-- Confere fisicamente ------->|
   |-- Registra por item: -------->|
   |   qtd recebida, lote,        |
   |   validade, divergências      |
   |-- Confirma recebimento ------>|-- Atualiza status do pedido
   |                               |-- Cria entradas no estoque (M03) com lotes
   |                               |-- Registra preços no histórico
   |                               |-- Gera conta a pagar (M08)
   |                               |-- Registra auditoria
   |<-- "Recebimento confirmado" --|
```

### 7.3 Comparação de Fornecedores

```
Dono/Admin                       Sistema
   |                               |
   |-- Seleciona insumo ---------->|
   |-- Acessa "Comparar Fornec." ->|-- Consulta histórico de preço
   |                               |-- Lista fornecedores com:
   |                               |   último preço, preço médio,
   |                               |   avaliação, prazo entrega
   |<-- Exibe tabela comparativa --|
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-CMP-01 | Lista de pedidos de compra: tabela com status colorido, fornecedor, valor, data |
| UI-CMP-02 | Formulário de pedido: seleção de fornecedor → lista de insumos que fornece com último preço |
| UI-CMP-03 | Tela de recebimento: checklist por item com campos qtd recebida, lote, validade |
| UI-CMP-04 | Gráfico de evolução de preço por insumo (últimos 6 meses) |
| UI-CMP-05 | Badge de divergência no pedido quando conferência tem diferenças |
