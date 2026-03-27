# SPEC-003 — Estoque e Controle de Validade

> **Módulo**: M03 – Estoque e Controle de Validade  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: ALTA — Controla o que entra, sai, vence e falta  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Controlar todo o fluxo de materiais da padaria — insumos de produção, produtos prontos e materiais de apoio/limpeza — com rastreabilidade de lotes, controle de validade, alertas de reposição e registro formal de perdas e desperdícios.

### Evidências

- **RF03**: "Registrar entrada e saída de estoque: motivo, quantidade, data, responsável."
- **RF04**: "Consultar itens em falta, abaixo do mínimo e com sobra excessiva."
- **RF05**: "Registrar perdas, desperdícios, vencimentos e descarte de mercadorias."
- **EST-01 a EST-06**: Perguntas de operabilidade sobre estoque.
- **Quadro de Problemas (Estoque)**: "Falta trigo, sobra sal; insumos comprados sem visão confiável do saldo real."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-EST-01** | Registrar entrada de estoque: item, quantidade, lote (opcional), data de validade, fornecedor, nota/recibo, responsável | RF03, EST-04 |
| **RF-EST-02** | Registrar saída de estoque: por venda (automática via M06), por produção (via M05), por perda/descarte, por transferência interna | RF03 |
| **RF-EST-03** | Consultar saldo atual por item: quantidade disponível, última entrada, última saída | RF04 |
| **RF-EST-04** | Alertas de estoque mínimo: notificação visual quando item atinge nível mínimo configurado | RF04, EST-03 |
| **RF-EST-05** | **Controle de validade por lote**: registrar data de validade na entrada, exibir itens próximos do vencimento | EST-06 |
| **RF-EST-06** | **Alerta de vencimento**: notificação com antecedência configurável (M13: `estoque.dias_alerta_validade`) | EST-06 |
| **RF-EST-07** | **PVPS** (Primeiro que Vence, Primeiro que Sai): sugerir saída pelo lote mais próximo do vencimento | Boas práticas |
| **RF-EST-08** | Registrar perdas e desperdícios: motivo, quantidade, valor estimado, responsável | RF05 |
| **RF-EST-09** | Registrar descarte formal com motivo e confirmação no sistema (assinatura digital simplificada) | RF05 |
| **RF-EST-10** | Inventário/contagem física: lançamento de contagem real vs. saldo sistema, apuração de diferenças | Controle |
| **RF-EST-11** | Classificação ABC automática dos itens (por valor de consumo mensal) | Gestão |
| **RF-EST-12** | Sugestão automática de compra: itens abaixo do mínimo ou previsão de falta baseada no consumo médio | EST-03 |
| **RF-EST-13** | Controle separado por tipo de estoque: insumos de produção, produtos prontos, materiais de apoio/limpeza | EST-01, EST-02 |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Dono / Gerente** | Acesso total. Aprova ajustes de inventário. Visualiza alertas e relatórios. |
| **Administrativo** | Registra entradas (recebimento), saídas, perdas. Executa inventário. |
| **Atendente** | Consulta disponibilidade de produto (leitura). |
| **Caixa** | Consulta preço/disponibilidade (leitura). |
| **Chapeiro** | Sem acesso direto (baixa de insumo ocorre via M05). |

---

## 4. Entidades e Dados Conceituais

### 4.1 Movimento de Estoque

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `insumo_id` | referência | Sim | Item movimentado (FK → insumo M01) |
| `tipo` | enum | Sim | `ENTRADA`, `SAIDA`, `PERDA`, `AJUSTE_POSITIVO`, `AJUSTE_NEGATIVO` |
| `motivo` | enum | Sim | `COMPRA`, `PRODUCAO`, `VENDA`, `DESCARTE_VENCIMENTO`, `DESCARTE_AVARIA`, `DESCARTE_PREPARO`, `TRANSFERENCIA`, `INVENTARIO`, `OUTRO` |
| `quantidade` | numérico | Sim | Quantidade movimentada (sempre positivo, direção pelo tipo) |
| `lote_id` | referência | Não | Lote associado (se controle por lote) |
| `fornecedor_id` | referência | Condicional | Obrigatório se motivo = COMPRA |
| `documento_ref` | texto | Não | Número da nota fiscal, recibo, pedido de compra |
| `custo_unitario` | monetário | Condicional | Custo unitário na entrada (para cálculo de custo médio) |
| `observacao` | texto | Não | Detalhamento |
| `responsavel_id` | referência | Sim | Quem registrou (FK → usuario) |
| `data_movimento` | timestamp | Sim | Quando ocorreu |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.2 Lote

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `insumo_id` | referência | Sim | Item deste lote |
| `codigo_lote` | texto | Não | Código do lote do fornecedor (se informado) |
| `data_fabricacao` | data | Não | Data de fabricação (se disponível) |
| `data_validade` | data | Sim | Data de validade |
| `quantidade_entrada` | numérico | Sim | Quantidade original do lote |
| `quantidade_atual` | numérico | Sim | Saldo atual do lote |
| `custo_unitario` | monetário | Não | Custo de aquisição unitário |
| `fornecedor_id` | referência | Não | Fornecedor de origem |
| `situacao` | enum | Sim | `DISPONIVEL`, `ESGOTADO`, `VENCIDO`, `DESCARTADO` |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.3 Saldo de Estoque (visão consolidada)

| Campo | Tipo | Descrição |
|---|---|---|
| `insumo_id` | referência | Item |
| `quantidade_total` | numérico | Σ de todos os lotes DISPONIVEL |
| `custo_medio` | monetário | Custo médio ponderado |
| `estoque_minimo` | numérico | Configurado no cadastro (M01) |
| `abaixo_minimo` | booleano | `quantidade_total < estoque_minimo` |
| `proximo_vencimento` | data | Menor data de validade entre lotes DISPONIVEL |

**Nota**: Saldo é uma **visão calculada** (view/cache), não tabela separada.

### 4.4 Inventário (Contagem Física)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `data` | data | Sim | Data do inventário |
| `responsavel_id` | referência | Sim | Quem realizou |
| `status` | enum | Sim | `EM_ANDAMENTO`, `FINALIZADO`, `APROVADO` |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.5 Item de Inventário

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `inventario_id` | referência | Sim | Inventário pai |
| `insumo_id` | referência | Sim | Item contado |
| `quantidade_sistema` | numérico | Sim | Saldo no sistema no momento da contagem |
| `quantidade_contada` | numérico | Sim | Quantidade real contada |
| `diferenca` | numérico | Sim | `contada - sistema` |
| `justificativa` | texto | Condicional | Obrigatório se diferença ≠ 0 |

---

## 5. Ciclo de Vida do Lote

```
DISPONIVEL → ESGOTADO (quantidade_atual = 0)
     ↓
VENCIDO (data_validade < hoje)
     ↓
DESCARTADO (após registro formal de descarte)
```

| Estado | Descrição |
|---|---|
| `DISPONIVEL` | Lote com saldo e dentro da validade. Elegível para saída PVPS. |
| `ESGOTADO` | Saldo zerado por consumo normal. |
| `VENCIDO` | Data de validade ultrapassada. Sistema marca automaticamente. Requer descarte. |
| `DESCARTADO` | Descarte formalizado no sistema com motivo e responsável. |

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-EST-01** | Toda entrada de item perecível deve registrar data de validade. Se não informada, preencher com `data_entrada + dias_validade_padrao` do cadastro. |
| **RN-EST-02** | **PVPS**: ao registrar saída, o sistema sugere/seleciona automaticamente o lote com data de validade mais próxima. |
| **RN-EST-03** | Saldo de estoque nunca pode ficar negativo fisicamente. Se a saída excede o saldo, bloquear com aviso. |
| **RN-EST-04** | Alerta de estoque mínimo: quando `quantidade_total ≤ estoque_minimo`, gerar notificação para Dono/Gerente/Admin. |
| **RN-EST-05** | Alerta de vencimento: quando `data_validade - hoje ≤ dias_alerta_validade` (M13), gerar notificação. |
| **RN-EST-06** | Lotes VENCIDOS devem ser marcados automaticamente pelo sistema (job diário ou on-demand). |
| **RN-EST-07** | Descarte de lote vencido exige registro formal: motivo, quantidade, responsável (gera movimento tipo PERDA). |
| **RN-EST-08** | Custo médio ponderado é recalculado a cada entrada: `((saldo_anterior × custo_anterior) + (qtd_entrada × custo_entrada)) / (saldo_anterior + qtd_entrada)`. |
| **RN-EST-09** | Ajustes de inventário (diferenças) geram movimentos de estoque tipo AJUSTE_POSITIVO ou AJUSTE_NEGATIVO com justificativa obrigatória. |
| **RN-EST-10** | **Classificação ABC**: A = top 20% itens que representam 80% do valor consumido; B = próximos 30%; C = restante 50%. Recalculada mensalmente. |
| **RN-EST-11** | Sugestão de compra = itens com `quantidade_total < estoque_minimo` OU `previsão de falta em X dias baseada no consumo médio dos últimos 30 dias`. |
| **RN-EST-12** | Todo movimento de estoque gera log de auditoria (M00). |
| **RN-EST-13** | Entrada por compra (recebimento M04) é a principal via de entrada. Entrada manual é possível mas gera alerta "sem documento fiscal". |

---

## 7. Fluxos Principais

### 7.1 Entrada de Estoque (Recebimento de Compra)

```
Administrativo                   Sistema
   |                               |
   |-- Recebe mercadoria física -->|
   |-- Acessa "Entrada Estoque" -->|
   |-- Seleciona pedido compra --->|-- Pré-preenche itens e quantidades
   |   (ou entrada manual)         |   do pedido
   |-- Confere item a item: ------>|
   |   quantidade recebida,        |
   |   lote, data validade,        |
   |   custo unitário              |
   |-- Registra divergências ----->|-- Marca diferenças (se houver)
   |-- Confirma recebimento ------>|-- Cria movimentos ENTRADA
   |                               |-- Cria/atualiza lotes
   |                               |-- Recalcula custo médio
   |                               |-- Atualiza saldo
   |                               |-- Gera conta a pagar (M08)
   |                               |-- Registra auditoria
   |<-- "Estoque atualizado" ------|
```

### 7.2 Verificação de Validade (Diária)

```
Sistema (automático/agendado)
   |
   |-- Consulta lotes DISPONIVEL
   |-- Para cada lote:
   |     Se data_validade < hoje:
   |       → Marca lote como VENCIDO
   |       → Gera alerta "Lote vencido: [item] - [lote]"
   |     Se data_validade - hoje ≤ dias_alerta:
   |       → Gera alerta "Vencimento próximo: [item] - [lote] - vence em X dias"
   |-- Notifica Dono/Gerente/Admin
```

### 7.3 Descarte de Produto Vencido

```
Administrativo                   Sistema
   |                               |
   |-- Acessa alertas de --------->|-- Lista lotes VENCIDOS
   |   "Produtos Vencidos"         |
   |-- Seleciona lote(s) --------->|
   |-- Registra descarte: -------->|
   |   quantidade, motivo          |
   |   (vencimento/avaria/outro)   |
   |-- Confirma ------------------>|-- Cria movimento tipo PERDA
   |                               |-- Marca lote como DESCARTADO
   |                               |-- Atualiza saldo
   |                               |-- Registra auditoria com valor estimado da perda
   |<-- "Descarte registrado" -----|
```

### 7.4 Inventário (Contagem Física)

```
Administrativo                   Sistema                      Dono/Gerente
   |                               |                             |
   |-- Inicia inventário --------->|-- Cria inventário           |
   |                               |   EM_ANDAMENTO              |
   |                               |-- Gera lista de itens       |
   |                               |   com saldo do sistema      |
   |-- Conta item a item: -------->|                             |
   |   quantidade real             |                             |
   |-- Finaliza contagem --------->|-- Calcula diferenças        |
   |                               |-- Status = FINALIZADO       |
   |                               |-- Envia para aprovação ---->|
   |                               |                             |-- Revisa diferenças
   |                               |                             |-- Aprova inventário
   |                               |<-- Status = APROVADO -------|
   |                               |-- Gera ajustes automáticos  |
   |                               |   (AJUSTE_POSITIVO/NEGATIVO)|
   |                               |-- Atualiza saldos           |
   |                               |-- Registra auditoria        |
```

### 7.5 Consulta de Sugestão de Compra

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Sugestão Compra" -->|
   |                               |-- Calcula consumo médio (30 dias)
   |                               |-- Identifica:
   |                               |   - Abaixo do mínimo agora
   |                               |   - Previsão de falta em X dias
   |                               |-- Lista com: item, saldo, consumo/dia,
   |                               |   dias até falta, quantidade sugerida,
   |                               |   fornecedor preferencial, último preço
   |<-- Exibe lista sugerida ------|
   |-- Seleciona itens ----------->|-- Gera pedido de compra (M04)
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-EST-01 | Dashboard de estoque: cards com "itens abaixo do mínimo", "itens vencendo", "itens vencidos", "último inventário" |
| UI-EST-02 | Lista de itens: tabela com nome, saldo, mínimo, situação, próximo vencimento — com cores (vermelho: crítico, amarelo: atenção, verde: ok) |
| UI-EST-03 | Entrada de estoque: formulário com leitor de código de barras (M14) e busca por nome |
| UI-EST-04 | Tela de inventário: lista com campo "contado" ao lado de "sistema" — cálculo automático de diferença |
| UI-EST-05 | Alerta visual permanente na barra do sistema quando há itens vencidos (badge vermelho) |
| UI-EST-06 | Histórico de movimentação por item: timeline com tipo, quantidade, responsável, data |
