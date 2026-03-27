# SPEC-007 — Caixa e Pagamentos

> **Módulo**: M07 – Caixa e Pagamentos  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: CRÍTICA — Sem caixa não há recebimento de vendas  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Controlar toda a movimentação financeira diária da padaria: abertura e fechamento formal de caixa, recebimento de pagamentos em múltiplas formas, sangrias, suprimentos e conferência. Interface com pedidos (M06), fiado (M08), vales (M09).

### Evidências

- **RF11**: "Registrar pagamentos por dinheiro, cartão, pix e fiado."
- **FIN-05**: "Quem pode tirar dinheiro do caixa? Poucas pessoas."
- **FIN-06**: "Registrar entradas, saídas, sangrias, vales, formas de pagamento. Comparar apurado × real."
- **FIN-07**: "Gastos frequentes afetam resultado."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-CX-01** | Abertura de caixa: valor de abertura (troco), operador responsável, data/hora | FIN-06 |
| **RF-CX-02** | Registro de pagamento de pedidos: dinheiro, cartão (débito/crédito), PIX, fiado, vale-refeição, misto | RF11 |
| **RF-CX-03** | Pagamento misto: dividir pagamento em múltiplas formas (ex.: R$20 PIX + R$15 dinheiro) | Realidade operacional |
| **RF-CX-04** | Sangria de caixa: retirada de dinheiro com motivo, valor, responsável, autorização | FIN-05 |
| **RF-CX-05** | Suprimento de caixa: entrada de dinheiro adicional (reforço de troco) | FIN-06 |
| **RF-CX-06** | Registro de vale de funcionário pelo caixa (gera saída de dinheiro + registro em M09) | FIN-04 |
| **RF-CX-07** | Fechamento de caixa: totalização por forma de pagamento, valor esperado vs. contado, diferença | FIN-06 |
| **RF-CX-08** | Conferência cega: operador informa valor contado ANTES de ver o valor esperado | FIN-06 |
| **RF-CX-09** | Relatório de movimentação do caixa: entradas, saídas, sangrias, suprimentos, por forma de pagamento | FIN-06 |
| **RF-CX-10** | Histórico de caixas: consultar fechamentos anteriores com detalhamento | Controle |
| **RF-CX-11** | Troco calculado automaticamente quando pagamento em dinheiro | Agilidade |
| **RF-CX-12** | Saldo atual estimado de dinheiro em caixa (gaveta) | FIN-06 |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Caixa** | Abre caixa, registra pagamentos, realiza sangrias (com autorização), fecha caixa. |
| **Dono / Gerente** | Autoriza sangrias acima do limite. Consulta e audita fechamentos. Pode abrir/fechar caixa. |
| **Atendente** | Sem acesso direto ao caixa. Encaminha pedido para pagamento. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Caixa (sessão de caixa)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `data` | data | Sim | Data de operação |
| `operador_id` | referência | Sim | Quem abriu o caixa (FK → usuario) |
| `valor_abertura` | monetário | Sim | Valor de troco na abertura |
| `status` | enum | Sim | `ABERTO`, `FECHADO` |
| `valor_esperado` | monetário | Não | Calculado no fechamento (abertura + entradas - saídas) |
| `valor_contado` | monetário | Não | Informado pelo operador no fechamento |
| `diferenca` | monetário | Não | `valor_contado - valor_esperado` |
| `data_abertura` | timestamp | Sim | Horário de abertura |
| `data_fechamento` | timestamp | Não | Horário de fechamento |
| `observacao_fechamento` | texto | Não | Justificativa de diferença ou observações |

### 4.2 Movimento de Caixa

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `caixa_id` | referência | Sim | Sessão de caixa (FK) |
| `tipo` | enum | Sim | `RECEBIMENTO`, `SANGRIA`, `SUPRIMENTO`, `VALE_FUNCIONARIO`, `DESPESA_RAPIDA` |
| `forma_pagamento_id` | referência | Condicional | Forma de pagamento (quando tipo = RECEBIMENTO) |
| `pedido_id` | referência | Condicional | Pedido vinculado (quando tipo = RECEBIMENTO) |
| `funcionario_id` | referência | Condicional | Funcionário (quando tipo = VALE_FUNCIONARIO) |
| `valor` | monetário | Sim | Valor positivo (a direção é determinada pelo tipo) |
| `motivo` | texto | Condicional | Obrigatório para SANGRIA, VALE_FUNCIONARIO, DESPESA_RAPIDA |
| `autorizado_por` | referência | Condicional | Quem autorizou (para sangria acima do limite) |
| `data_hora` | timestamp | Sim | Momento do movimento |
| `operador_id` | referência | Sim | Quem registrou |

### 4.3 Pagamento (detalhe de pagamento de pedido)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `pedido_id` | referência | Sim | Pedido sendo pago |
| `caixa_id` | referência | Sim | Sessão de caixa |
| `forma_pagamento_id` | referência | Sim | Forma de pagamento utilizada |
| `valor` | monetário | Sim | Valor pago nesta forma |
| `cliente_id` | referência | Condicional | Cliente (obrigatório se fiado) |
| `troco` | monetário | Não | Troco dado (se dinheiro) |
| `data_hora` | timestamp | Sim | Momento do pagamento |
| `operador_id` | referência | Sim | Caixa que registrou |

**Nota**: Um pedido pode ter múltiplos registros de pagamento (pagamento misto).

---

## 5. Ciclo de Vida do Caixa

```
ABERTO → FECHADO
```

| Estado | Descrição |
|---|---|
| `ABERTO` | Caixa ativo, aceitando movimentações. |
| `FECHADO` | Caixa encerrado. Não aceita mais movimentações. Dados consolidados. |

### Regras de Transição

- Só pode existir **um caixa ABERTO por vez** no sistema.
- Abertura de novo caixa requer que o anterior esteja FECHADO.
- Fechamento exige que todos os pedidos ENTREGUES estejam FINALIZADOS ou CANCELADOS.
- Caixa FECHADO é imutável (não pode reabrir).

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-CX-01** | Só pode existir um caixa ABERTO por vez. Para abrir novo, fechar o anterior. |
| **RN-CX-02** | Sangria acima do limite configurado (M13: `caixa.limite_sangria_sem_aprovacao`) exige autorização de Dono/Gerente. |
| **RN-CX-03** | Conferência cega: sistema oculta o valor esperado até que o operador informe o valor contado. |
| **RN-CX-04** | Pagamento em fiado exige identificação do cliente (FK para M02.cliente). |
| **RN-CX-05** | Pagamento em fiado incrementa saldo devedor do cliente (integração com M08). |
| **RN-CX-06** | Se cliente atingiu limite de crédito fiado, bloquear pagamento em fiado (exige aprovação Dono/Gerente para exceção). |
| **RN-CX-07** | Troco é calculado automaticamente: `troco = valor_pago_dinheiro - valor_pedido_em_dinheiro`. |
| **RN-CX-08** | Valor de abertura de caixa usa padrão de M13 (`caixa.troco_padrao`) mas pode ser alterado pelo operador. |
| **RN-CX-09** | Todo movimento de caixa gera log de auditoria (M00). |
| **RN-CX-10** | Vale de funcionário: gera simultaneamente registro em M09 (vale) e saída no caixa. |
| **RN-CX-11** | Pagamento misto: a soma dos valores por forma deve ser ≥ valor total do pedido. |
| **RN-CX-12** | Diferença de caixa (sobra ou falta) é registrada mas não gera bloqueio — apenas alerta visual. |
| **RN-CX-13** | Gaveta estimada = abertura + recebimentos em dinheiro - sangrias - vales em dinheiro - troco dado. |

---

## 7. Fluxos Principais

### 7.1 Abertura de Caixa

```
Caixa                            Sistema
   |                               |
   |-- Acessa "Abrir Caixa" ------>|-- Verifica se há caixa ABERTO
   |                               |-- Se sim: "Feche o caixa anterior"
   |                               |-- Se não: exibe formulário
   |-- Informa valor de troco ---->|
   |   (pré-preenchido com padrão) |
   |-- Confirma abertura --------->|-- Cria caixa com status=ABERTO
   |                               |-- Registra auditoria
   |<-- "Caixa aberto. Bom dia!" -|
```

### 7.2 Pagamento de Pedido

```
Caixa                            Sistema
   |                               |
   |-- Seleciona pedido ENTREGUE ->|-- Exibe resumo: itens + total
   |   (ou comanda FECHADA)        |
   |-- Escolhe forma(s) de ------->|
   |   pagamento                   |
   |   [Dinheiro: R$50]            |
   |   [PIX: R$20]                 |
   |                               |-- Valida: soma ≥ total
   |                               |-- Se dinheiro: calcula troco
   |                               |-- Se fiado: verifica cliente + limite
   |-- Confirma pagamento -------->|-- Registra pagamento(s)
   |                               |-- Gera movimentos de caixa
   |                               |-- Atualiza pedido → FINALIZADO
   |                               |-- Se fiado: incrementa saldo M08
   |                               |-- Registra auditoria
   |<-- "Pago! Troco: R$X,XX" ----|
```

### 7.3 Sangria

```
Caixa                            Sistema
   |                               |
   |-- Acessa "Sangria" ---------->|
   |-- Informa valor + motivo ---->|-- Verifica valor vs. limite
   |                               |-- Se acima do limite:
   |                               |     solicita autorização
Dono/Gerente                       |
   |-- Confirma (senha/PIN) ------>|
   |                               |-- Registra sangria
   |                               |-- Atualiza gaveta estimada
   |                               |-- Registra auditoria
   |<-- "Sangria registrada" ------|
```

### 7.4 Fechamento de Caixa (Conferência Cega)

```
Caixa                            Sistema
   |                               |
   |-- Acessa "Fechar Caixa" ----->|-- Verifica pedidos pendentes
   |                               |-- Se há pendente: alerta
   |                               |-- Exibe formulário de contagem
   |                               |   (NÃO mostra valor esperado)
   |-- Conta dinheiro, informa: -->|
   |   "Contei R$ 1.250,00"       |
   |-- Confirma contagem --------->|-- Calcula valor esperado
   |                               |-- Calcula diferença
   |                               |-- Exibe: "Esperado: R$1.280,00
   |                               |           Contado:  R$1.250,00
   |                               |           Falta:    R$ 30,00"
   |-- [Observação sobre falta] -->|
   |-- Confirma fechamento ------->|-- Fecha caixa
   |                               |-- Consolida totais por forma pgto
   |                               |-- Registra auditoria
   |<-- "Caixa fechado" -----------|
   |                               |-- Gera resumo imprimível
```

---

## 8. Resumo de Fechamento (estrutura)

| Informação | Cálculo |
|---|---|
| **Total de recebimentos** | Σ movimentos tipo RECEBIMENTO |
| **Por forma de pagamento** | Agrupado por forma_pagamento |
| **Sangrias** | Σ movimentos tipo SANGRIA |
| **Suprimentos** | Σ movimentos tipo SUPRIMENTO |
| **Vales** | Σ movimentos tipo VALE_FUNCIONARIO |
| **Despesas rápidas** | Σ movimentos tipo DESPESA_RAPIDA |
| **Gaveta esperada** | Abertura + Dinheiro recebido - Sangrias - Vales em dinheiro - Troco dado + Suprimentos |
| **Gaveta contada** | Informado pelo operador |
| **Diferença** | Contada - Esperada |
| **Qtd pedidos finalizados** | Count pedidos FINALIZADOS no período do caixa |
| **Ticket médio** | Total recebimentos / Qtd pedidos |

---

## 9. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-CX-01 | Tela principal do caixa: lista de pedidos ENTREGUE esperando pagamento + botão para cada ação rápida |
| UI-CX-02 | Seleção de forma de pagamento com botões grandes e ícones (💵 Dinheiro, 💳 Cartão, 📱 PIX, 📝 Fiado) |
| UI-CX-03 | Calculadora de troco: campo "Recebido" → mostra troco automaticamente |
| UI-CX-04 | Indicador de gaveta estimada visível na tela principal do caixa |
| UI-CX-05 | Resumo de fechamento: tabela clara + botão "Imprimir" |
| UI-CX-06 | Conferência cega: campo de contagem SEM exibir o valor esperado até a confirmação |
