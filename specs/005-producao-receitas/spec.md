# SPEC-005 — Produção e Receitas

> **Módulo**: M05 – Produção e Receitas  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Controlar o que é produzido na padaria, vinculando produtos finais a fichas técnicas (receitas) com ingredientes e quantidades. Permite calcular custo de produção, consumir estoque automaticamente e planejar a produção com base no histórico de vendas.

### Evidências

- **Quadro de Problemas (Produção)**: "Sem controle do que foi produzido, consumido ou desperdiçado."
- **PL-07**: "O dono quer controlar receita por produto ou apenas vendas totais?"
- **DP-RECEITA-001**: Decisão pendente sobre granularidade — premissa: controlar por produto.
- **EST-05**: "Registro simples de perdas, sobras e descarte. Perecíveis precisam desse controle."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-PRD-01** | Cadastrar ficha técnica: produto final, lista de ingredientes com quantidade por unidade produzida, modo de preparo simplificado | PL-07 |
| **RF-PRD-02** | Calcular custo unitário do produto a partir da ficha técnica (soma dos custos dos insumos por unidade produzida) | Gestão de custos |
| **RF-PRD-03** | Registrar ordem de produção: o que produzir, quantidade planejada, quantidade real, perdas | EST-05 |
| **RF-PRD-04** | Baixa automática de estoque de insumos ao confirmar produção (consumo = ficha técnica × qtd produzida) | EST-01 |
| **RF-PRD-05** | Entrada automática no estoque de produtos prontos após produção confirmada | EST-01 |
| **RF-PRD-06** | Registrar perdas de produção: produto queimado, massa ruim, descarte pós-preparo, com motivo e quantidade | EST-05 |
| **RF-PRD-07** | Histórico de produção: o que foi produzido, quando, por quem, quanto custou | Controle |
| **RF-PRD-08** | Sugestão de produção: baseada na média de vendas dos últimos X dias (configurável em M13) | Planejamento |
| **RF-PRD-09** | Alerta de insumo insuficiente antes de confirmar produção (verificar estoque vs. ficha técnica × quantidade) | Prevenção |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono / Gerente** | Cria/edita fichas técnicas. Define margens. Consulta custos e histórico. |
| **Administrativo** | Registra ordens de produção. Gerencia fichas técnicas. |
| **Chapeiro / Padeiro** | Consulta ficha técnica (modo de preparo). Pode registrar produção e perdas. |
| **Atendente / Caixa** | Sem acesso direto. Beneficiam-se do cálculo de disponibilidade. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Ficha Técnica (Receita)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `produto_id` | referência | Sim | Produto final (FK → M01.produto) |
| `rendimento` | numérico | Sim | Quantidade que a ficha produz (ex.: 50 pães) |
| `unidade_rendimento_id` | referência | Sim | Unidade do rendimento (un, kg, etc.) |
| `modo_preparo` | texto | Não | Descrição simplificada do modo de preparo |
| `tempo_preparo_minutos` | inteiro | Não | Tempo estimado de preparo |
| `custo_total` | monetário | Calculado | Σ(custo ingrediente × quantidade) |
| `custo_unitario` | monetário | Calculado | `custo_total / rendimento` |
| `margem_percentual` | numérico | Não | Margem sobre o custo (padrão: M13.`producao.margem_padrao_percentual`) |
| `preco_sugerido` | monetário | Calculado | `custo_unitario × (1 + margem/100)` |
| `situacao` | enum | Sim | `ATIVA`, `INATIVA` |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.2 Ingrediente da Ficha Técnica

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `ficha_tecnica_id` | referência | Sim | Ficha técnica pai |
| `insumo_id` | referência | Sim | Insumo/ingrediente (FK → M01.insumo) |
| `quantidade` | numérico | Sim | Quantidade para o rendimento total da ficha |
| `unidade_medida_id` | referência | Sim | Unidade de medida |
| `custo_unitario_insumo` | monetário | Calculado | Custo médio atual do insumo (M03) |
| `custo_total_ingrediente` | monetário | Calculado | `quantidade × custo_unitario_insumo` |

### 4.3 Ordem de Produção

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `numero` | sequencial | Sim | Número da ordem |
| `ficha_tecnica_id` | referência | Sim | Ficha técnica usada |
| `quantidade_planejada` | numérico | Sim | Quantidade a produzir (em unidades do produto) |
| `quantidade_produzida` | numérico | Não | Quantidade efetivamente produzida |
| `quantidade_perda` | numérico | Não | Quantidade perdida no processo |
| `motivo_perda` | texto | Condicional | Motivo da perda (se quantidade_perda > 0) |
| `status` | enum | Sim | `PLANEJADA`, `EM_PRODUCAO`, `CONCLUIDA`, `CANCELADA` |
| `responsavel_id` | referência | Sim | Quem executou/registrou |
| `data_planejada` | data | Sim | Data para produção |
| `data_inicio` | timestamp | Não | Quando iniciou |
| `data_conclusao` | timestamp | Não | Quando concluiu |
| `custo_total` | monetário | Calculado | baseado na ficha × quantidade produzida |
| `observacao` | texto | Não | Observações |
| `criado_em` | timestamp | Sim | Auditoria |

---

## 5. Ciclo de Vida da Ordem de Produção

```
PLANEJADA → EM_PRODUCAO → CONCLUIDA
     ↓            ↓
  CANCELADA   CANCELADA
```

| Estado | Descrição |
|---|---|
| `PLANEJADA` | Ordem criada, produção não iniciou. Pode ser editada ou cancelada. |
| `EM_PRODUCAO` | Produção iniciada. Insumos verificados (alerta de insuficiência já emitido). |
| `CONCLUIDA` | Produção encerrada. Quantidade produzida e perdas registradas. Estoque atualizado. |
| `CANCELADA` | Ordem cancelada antes ou durante produção. Motivo obrigatório. |

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-PRD-01** | Custo da ficha técnica é recalculado sempre que consultado (usa custo médio atual dos insumos do M03). |
| **RN-PRD-02** | Ao concluir ordem de produção: baixa automática de insumos no estoque (M03) proporcional à quantidade produzida. |
| **RN-PRD-03** | Ao concluir ordem de produção: entrada automática do produto pronto no estoque (M03). |
| **RN-PRD-04** | Antes de iniciar produção, o sistema verifica estoque de cada insumo: se insuficiente, exibe alerta (não bloqueia, apenas avisa). |
| **RN-PRD-05** | Ficha técnica com insumos sem estoque suficiente exibe indicador visual de "estoque insuficiente para X unidades". |
| **RN-PRD-06** | Sugestão de produção = média de vendas do produto nos últimos X dias (M13: `producao.dias_historico_sugestao`), arredondada para cima. |
| **RN-PRD-07** | Perda de produção gera automaticamente movimento de PERDA no estoque (M03) para os insumos proporcionais. |
| **RN-PRD-08** | Preço sugerido = `custo_unitario × (1 + margem/100)`. Serve de referência, não altera preço de venda automaticamente. |
| **RN-PRD-09** | Ficha técnica inativa não aparece para seleção em novas ordens de produção. |
| **RN-PRD-10** | Todo registro gera log de auditoria (M00). |

---

## 7. Fluxos Principais

### 7.1 Criar Ficha Técnica

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Nova Ficha" ------->|
   |-- Seleciona produto final --->|
   |-- Define rendimento: -------->|  (ex.: 50 pães franceses)
   |   quantidade + unidade        |
   |-- Adiciona ingredientes: ---->|
   |   Trigo 25kg, Sal 500g,      |
   |   Fermento 750g, Água 15L    |
   |-- [Modo de preparo] --------->|  (texto livre)
   |-- Salva ---------------------->|-- Calcula custo total e unitário
   |                               |-- Calcula preço sugerido com margem
   |                               |-- Registra auditoria
   |<-- "Ficha criada.            --|
   |     Custo: R$X,XX/un         |
   |     Sugerido: R$Y,YY/un"     |
```

### 7.2 Registrar Produção

```
Padeiro/Admin                    Sistema
   |                               |
   |-- Acessa "Nova Produção" ---->|
   |-- Seleciona ficha técnica --->|
   |-- Informa qtd planejada ----->|-- Verifica estoque de insumos
   |                               |-- Se insuficiente: alerta
   |                               |   "Falta Xkg de trigo"
   |-- Confirma início ----------->|-- Status = EM_PRODUCAO
   |                               |
   |   (após produção física)      |
   |                               |
   |-- Informa qtd produzida ----->|
   |-- Informa perdas (se houver)->|  + motivo
   |-- Confirma conclusão -------->|-- Baixa insumos do estoque (M03)
   |                               |-- Entrada produto pronto (M03)
   |                               |-- Se houve perda: registra PERDA
   |                               |-- Calcula custo real
   |                               |-- Registra auditoria
   |<-- "Produção concluída.      --|
   |     Custo real: R$X,XX"      |
```

### 7.3 Sugestão de Produção

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Sugestão Produção"->|
   |                               |-- Calcula média de vendas por produto
   |                               |   (últimos X dias — M13)
   |                               |-- Verifica saldo de produto pronto
   |                               |-- Lista:
   |                               |   Produto | Média/dia | Saldo | Sugestão
   |                               |   Pão Fr  |    200    |   30  |    170
   |                               |   Bolo    |     15    |    2  |     13
   |<-- Exibe tabela sugestão -----|
   |-- Seleciona e gera ordens --->|-- Cria ordens PLANEJADA
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-PRD-01 | Lista de fichas técnicas: produto, rendimento, custo unitário, preço sugerido, situação |
| UI-PRD-02 | Formulário de ficha técnica: tabela de ingredientes com busca e cálculo automático de custo |
| UI-PRD-03 | Tela de produção: cards de ordens com status colorido (azul: planejada, amarelo: em produção, verde: concluída) |
| UI-PRD-04 | Alerta visual na tela de produção quando estoque insuficiente (ícone vermelho no ingrediente) |
| UI-PRD-05 | Comparativo custo vs. preço de venda: indicador de margem real (verde: positiva, vermelho: negativa) |
