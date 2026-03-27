# SPEC-008 — Financeiro Básico

> **Módulo**: M08 – Financeiro Básico  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: ALTA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Dar ao dono visão financeira clara e simplificada: contas a pagar (fornecedores, despesas), contas a receber (fiado), gestão completa de fiado, **emissão e baixa de boletos bancários (Bradesco)** para cobrança de contas de clientes, vales de funcionário como despesa, fluxo de caixa projetado e DRE simplificado.

### Evidências

- **RF12**: "Manter cadastro de clientes fiado: histórico de débitos, pagamentos e saldo pendente."
- **RF13**: "Registrar retirada de vale para funcionário."
- **FIN-01 a FIN-07**: Todas as perguntas de fiado, caixa e financeiro.
- **OE-06**: "Dar ao dono visão clara do que entra, sai, falta, sobra e gera prejuízo."
- **ATD-08**: "Sistema deve separar vendas iFood e cruzar faturamento com taxas/embalagem/custos."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-FIN-01** | Contas a pagar: registrar débito com fornecedor/prestador — valor, vencimento, forma pagamento, status | FIN-07 |
| **RF-FIN-02** | Contas a receber: registrar crédito de clientes fiado | RF12, FIN-01 |
| **RF-FIN-03** | Baixa de conta a pagar: registro de pagamento efetuado com data, valor, forma | FIN-07 |
| **RF-FIN-04** | Baixa de conta a receber: registro de recebimento (pagamento do fiado) | FIN-03 |
| **RF-FIN-05** | Gestão de fiado completa: registro por cliente, pagamentos parciais, saldo devedor, histórico | RF12, FIN-01 |
| **RF-FIN-06** | Alerta de contas a pagar próximas do vencimento (antecedência configurável em M13) | Controle |
| **RF-FIN-07** | Alerta de fiados em atraso: faixas 7 dias, 15 dias, 30+ dias | FIN-03 |
| **RF-FIN-08** | Fluxo de caixa simplificado: entradas e saídas previstas e realizadas por período | OE-06 |
| **RF-FIN-09** | DRE simplificado: receitas, custos de mercadoria, despesas operacionais, resultado | OE-06 |
| **RF-FIN-10** | Categorização de despesas: matéria-prima, manutenção, limpeza, taxa delivery, salários, aluguel, água, luz, gás | FIN-07 |
| **RF-FIN-11** | Despesas recorrentes: lançamento automático mensal (aluguel, energia, água, gás) | FIN-07 |
| **RF-FIN-12** | Análise de custo delivery: receita vs. taxas/embalagem/custos por plataforma | ATD-08 |
| **RF-FIN-13** | Vales de funcionário como saída financeira: integração automática com M09 | FIN-04 |
| **RF-FIN-14** | Conciliação de caixa: cruzar fechamento de caixa (M07) com movimentação financeira | FIN-06 |
| **RF-FIN-15** | Emissão de boleto bancário (Bradesco) para cobrança de fiado: individual por conta ou consolidado por cliente (mensal) | Cobrança |
| **RF-FIN-16** | Baixa automática de boleto via arquivo de retorno CNAB 400 do Bradesco | Cobrança |
| **RF-FIN-17** | Impressão de boleto: formato padrão FEBRABAN com código de barras e linha digitável | Cobrança |
| **RF-FIN-18** | Geração em lote de boletos: selecionar múltiplos clientes com saldo devedor e gerar boletos de uma vez | Cobrança |
| **RF-FIN-19** | Consulta de situação dos boletos: gerado, registrado, pago, vencido, cancelado | Cobrança |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono** | Acesso total. Vê relatórios financeiros, DRE, fluxo de caixa. Gerencia fiado. |
| **Gerente** | Acesso total exceto configurações de despesas recorrentes. |
| **Caixa** | Registra pagamento de fiado (baixa manual). Consulta saldo de cliente. |
| **Administrativo** | Gera boletos, processa arquivos de retorno, consulta situação de boletos. |
| **Demais** | Sem acesso. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Conta a Pagar

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `descricao` | texto | Sim | Descrição da conta (ex.: "Compra trigo - Dist. Silva") |
| `fornecedor_id` | referência | Não | Fornecedor (se aplicável) |
| `pedido_compra_id` | referência | Não | Pedido de compra de origem (se gerado pelo M04) |
| `categoria` | enum | Sim | `MATERIA_PRIMA`, `MANUTENCAO`, `LIMPEZA`, `TAXA_DELIVERY`, `SALARIO`, `ALUGUEL`, `AGUA`, `LUZ`, `GAS`, `OUTRO` |
| `valor` | monetário | Sim | Valor total da conta |
| `valor_pago` | monetário | Sim | Valor já pago (default: 0) |
| `saldo` | monetário | Calculado | `valor - valor_pago` |
| `data_emissao` | data | Sim | Data de emissão da conta |
| `data_vencimento` | data | Sim | Data de vencimento |
| `data_pagamento` | data | Não | Data em que foi paga integralmente |
| `status` | enum | Sim | `PENDENTE`, `PAGO_PARCIAL`, `PAGO`, `ATRASADO`, `CANCELADO` |
| `recorrente` | booleano | Sim | Se é despesa recorrente |
| `observacao` | texto | Não | Observações |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.2 Conta a Receber (Fiado)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `cliente_id` | referência | Sim | Cliente devedor (FK → M02) |
| `pedido_id` | referência | Não | Pedido que originou o fiado (se aplicável) |
| `descricao` | texto | Sim | Descrição (ex.: "Compra dia 15/03") |
| `valor` | monetário | Sim | Valor total do fiado |
| `valor_recebido` | monetário | Sim | Valor já recebido (default: 0) |
| `saldo` | monetário | Calculado | `valor - valor_recebido` |
| `data_registro` | data | Sim | Data do fiado |
| `data_vencimento` | data | Não | Data combinada para pagamento |
| `data_quitacao` | data | Não | Data em que foi quitado |
| `status` | enum | Sim | `PENDENTE`, `PAGO_PARCIAL`, `QUITADO`, `CANCELADO` |
| `registrado_por` | referência | Sim | Quem registrou (FK → usuario) |
| `observacao` | texto | Não | Observações |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.3 Pagamento de Conta (baixa parcial ou total)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `tipo` | enum | Sim | `PAGAR` ou `RECEBER` |
| `conta_pagar_id` | referência | Condicional | FK se tipo = PAGAR |
| `conta_receber_id` | referência | Condicional | FK se tipo = RECEBER |
| `valor` | monetário | Sim | Valor do pagamento/recebimento |
| `forma_pagamento` | texto | Sim | Forma utilizada |
| `data` | data | Sim | Data do pagamento |
| `responsavel_id` | referência | Sim | Quem registrou |
| `observacao` | texto | Não | Observações |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.4 Boleto Bancário

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `cliente_id` | referência | Sim | Cliente devedor (FK → M02) |
| `nosso_numero` | texto | Sim | Número de controle do boleto no banco (gerado sequencialmente) |
| `numero_documento` | texto | Sim | Número do documento para o cliente |
| `contas_receber_ids` | lista referências | Sim | Contas a receber (fiados) cobertas por este boleto |
| `valor` | monetário | Sim | Valor do boleto |
| `data_emissao` | data | Sim | Data de emissão |
| `data_vencimento` | data | Sim | Data de vencimento |
| `data_pagamento` | data | Não | Data em que foi pago (vindo do retorno bancário) |
| `valor_pago` | monetário | Não | Valor efetivamente pago (pode incluir juros/multa) |
| `juros_multa` | monetário | Não | Valor de juros + multa recebido |
| `linha_digitavel` | texto | Calculado | Linha digitável para pagamento |
| `codigo_barras` | texto | Calculado | Código de barras no padrão FEBRABAN |
| `status` | enum | Sim | `GERADO`, `REGISTRADO`, `PAGO`, `VENCIDO`, `CANCELADO` |
| `arquivo_remessa_id` | referência | Não | Arquivo de remessa em que foi enviado |
| `arquivo_retorno_id` | referência | Não | Arquivo de retorno que confirmou pagamento |
| `instrucao_1` | texto | Não | Instrução impressa no boleto (ex.: "Não receber após 30 dias do vencimento") |
| `instrucao_2` | texto | Não | Segunda instrução |
| `observacao` | texto | Não | Observações internas |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.5 Arquivo de Remessa/Retorno

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `tipo` | enum | Sim | `REMESSA`, `RETORNO` |
| `nome_arquivo` | texto | Sim | Nome do arquivo (ex.: CB260326.REM) |
| `data_geracao` | timestamp | Sim | Data/hora de geração ou importação |
| `qtd_boletos` | inteiro | Sim | Quantidade de boletos no arquivo |
| `valor_total` | monetário | Sim | Soma dos valores dos boletos |
| `processado` | booleano | Sim | Se o retorno já foi processado |
| `gerado_por` | referência | Sim | Quem gerou/importou |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.6 Despesa Recorrente (template)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `descricao` | texto | Sim | Ex.: "Aluguel", "Conta de luz" |
| `categoria` | enum | Sim | Categoria da despesa |
| `valor_estimado` | monetário | Sim | Valor médio mensal |
| `dia_vencimento` | inteiro | Sim | Dia do mês para vencimento (1-31) |
| `ativa` | booleano | Sim | Se está ativa para geração automática |

---

## 5. Gestão de Fiado — Detalhamento

### 5.1 Visão do Cliente Fiado

| Informação | Origem |
|---|---|
| Nome / Apelido | M02.cliente |
| Telefone / WhatsApp | M02.cliente |
| Limite de crédito | M02.cliente (ou M13 padrão) |
| Saldo devedor total | Σ contas_receber com status PENDENTE ou PAGO_PARCIAL |
| Crédito disponível | `limite - saldo_devedor` |
| Fiados em aberto | Lista de contas_receber pendentes |
| Histórico de pagamentos | Lista de pagamentos vinculados |
| Dias em atraso (maior) | `MAX(hoje - data_vencimento)` para contas pendentes vencidas |
| Autorizados | Lista de dependentes (M02) |

### 5.2 Faixas de Atraso

| Faixa | Cor | Ação |
|---|---|---|
| Sem atraso (dentro do prazo) | Verde | Normal |
| 1-7 dias | Amarelo | Alerta leve |
| 8-15 dias | Laranja | Alerta moderado |
| 16-30 dias | Vermelho | Alerta crítico |
| 30+ dias | Vermelho escuro | Alerta crítico + notificação ao Dono |

### 5.3 Decisão Pendente

> **DP-FIADO-001**: Fiado para qualquer pessoa ou apenas clientes cadastrados?
> **Premissa adotada**: Fiado apenas para clientes cadastrados com `compra_fiado = true`. Sem cadastro, sem fiado.

---

## 6. Fluxo de Caixa Simplificado

### Estrutura

| Linha | Origem |
|---|---|
| **Entradas realizadas** | Vendas (M07), recebimentos de fiado (M08) |
| **Entradas previstas** | Fiados com vencimento futuro |
| **Saídas realizadas** | Compras pagas (M04), despesas pagas, vales (M09) |
| **Saídas previstas** | Contas a pagar com vencimento futuro, despesas recorrentes |
| **Saldo projetado** | Entradas - Saídas por dia/semana |

---

## 7. DRE Simplificado

| Linha | Cálculo |
|---|---|
| **Receita Bruta** | Σ vendas no período (M07) |
| (-) Devoluções/cancelamentos | Σ pedidos cancelados com valor |
| **= Receita Líquida** | |
| (-) CMV (Custo Mercadoria Vendida) | Σ custo dos insumos consumidos (M03/M05) |
| **= Lucro Bruto** | |
| (-) Despesas Operacionais | Σ contas pagas por categoria |
|   - Salários + vales | M09 |
|   - Aluguel, água, luz, gás | M08 recorrentes |
|   - Manutenção | M11 |
|   - Limpeza | M10 |
|   - Taxas delivery | M08 delivery |
|   - Outras | M08 |
| **= Resultado Operacional** | |
| (-) Perdas e desperdícios | Valor estimado das perdas (M03) |
| **= Resultado Líquido** | |

---

## 8. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-FIN-01** | Fiado só pode ser registrado para cliente com `compra_fiado = true` e crédito disponível. |
| **RN-FIN-02** | Se saldo devedor do cliente + novo fiado > limite de crédito → bloquear. Exceção: aprovação do Dono/Gerente. |
| **RN-FIN-03** | Pagamento parcial de fiado: atualiza `valor_recebido` e `saldo`. Se `saldo = 0`, status = QUITADO. |
| **RN-FIN-04** | Conta a pagar gerada automaticamente por recebimento de compra (M04) tem referência ao pedido de compra. |
| **RN-FIN-05** | Contas a pagar vencidas (data_vencimento < hoje E status ≠ PAGO) são marcadas como ATRASADO automaticamente. |
| **RN-FIN-06** | Despesas recorrentes: no último dia do mês, o sistema gera automaticamente contas a pagar para o mês seguinte. |
| **RN-FIN-07** | Vale de funcionário (M09) gera automaticamente uma saida financeira categorizada como despesa de pessoal. |
| **RN-FIN-08** | Análise de delivery: receita da plataforma - (taxa configurada em M13 × valor) = lucro estimado. |
| **RN-FIN-09** | Cancelamento de conta a pagar/receber exige motivo e registra auditoria (M00). |
| **RN-FIN-10** | DRE e fluxo de caixa são visualizações calculadas, não dados persistidos (sempre em tempo real). |
| **RN-FIN-11** | Todo registro financeiro gera log de auditoria (M00). |
| **RN-FIN-12** | Boleto só pode ser emitido para cliente cadastrado com CPF/CNPJ válido (obrigatório para registro bancário). |
| **RN-FIN-13** | Boleto consolidado mensal: agrupa todos os fiados pendentes do cliente em um único boleto. |
| **RN-FIN-14** | Nosso número é sequencial e único, com dígito verificador calculado conforme especificação Bradesco (carteira 09). |
| **RN-FIN-15** | Arquivo de remessa gerado no formato CNAB 400 — padrão Bradesco, com header, detalhes e trailer. |
| **RN-FIN-16** | Arquivo de retorno CNAB 400: ao importar, o sistema processa cada registro, identifica o boleto pelo nosso número e atualiza status/valor pago/data pagamento. |
| **RN-FIN-17** | Baixa automática de boleto pago: ao processar retorno, se boleto PAGO, o sistema faz baixa nas contas a receber vinculadas e atualiza saldo do cliente. |
| **RN-FIN-18** | Juros/multa configuráveis em M13: juros ao mês (padrão 1%), multa por atraso (padrão 2%). Impressos no boleto como instrução. |
| **RN-FIN-19** | Boleto vencido há mais de X dias (configurável em M13, padrão 60) pode ser cancelado automaticamente. |
| **RN-FIN-20** | É proibido alterar um boleto após status REGISTRADO. Para correção, deve-se cancelar e emitir novo. |

---

## 9. Fluxos Principais

### 9.1 Registrar Venda como Fiado

```
Caixa                            Sistema
   |                               |
   |-- Pedido para pagamento ----->|
   |-- Forma: FIADO -------------->|-- Solicita identificação do cliente
   |-- Busca cliente por nome ---->|-- Valida: compra_fiado = true?
   |                               |-- Valida: crédito disponível?
   |                               |-- Se bloqueado: "Limite excedido"
   |-- [Dono aprova exceção] ----->|-- (se necessário)
   |-- Confirma ------------------>|-- Cria conta_receber
   |                               |-- Atualiza saldo do cliente
   |                               |-- Pedido → FINALIZADO
   |                               |-- Registra auditoria
   |<-- "Fiado registrado. --------|
   |     Saldo do cliente: R$X" ---|
```

### 9.2 Pagamento de Fiado

```
Caixa/Dono                       Sistema
   |                               |
   |-- Acessa "Receber Fiado" ---->|
   |-- Busca cliente -------------->|-- Exibe saldo devedor + fiados abertos
   |-- Seleciona: pagar total ---->|  (ou valor parcial)
   |   ou informar valor           |
   |-- Informa forma pagamento --->|
   |-- Confirma ------------------>|-- Cria registro de pagamento
   |                               |-- Atualiza valor_recebido/saldo
   |                               |-- Se saldo = 0: status = QUITADO
   |                               |-- Registra auditoria
   |<-- "Recebido R$X. ------------|
   |     Saldo restante: R$Y" -----|
```

### 9.3 Gerar Despesas Recorrentes

```
Sistema (job mensal ou manual)
   |
   |-- Consulta despesas recorrentes ativas
   |-- Para cada uma:
   |     Cria conta_a_pagar com:
   |       data_vencimento = dia_vencimento do próximo mês
   |       valor = valor_estimado
   |       status = PENDENTE
   |     Registra auditoria
   |-- Notifica Dono: "X despesas geradas para o mês"
```

### 9.4 Gerar Boleto para Cliente (Individual ou Consolidado)

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Gerar Boleto" ----->|
   |-- Busca cliente -------------->|-- Exibe fiados pendentes com saldo
   |-- Seleciona: ------------->|
   |   [ ] Todos (consolidado)     |
   |   [x] Fiado #123 — R$80      |
   |   [x] Fiado #125 — R$45      |
   |-- Define vencimento --------->|
   |-- Confirma ------------------>|-- Valida CPF/CNPJ do cliente
   |                               |-- Gera nosso_numero sequencial
   |                               |-- Calcula código de barras + linha digitável
   |                               |-- Cria boleto (status = GERADO)
   |                               |-- Vincula contas_receber selecionadas
   |                               |-- Registra auditoria
   |<-- "Boleto gerado: R$125" ----|  
   |-- [Imprimir] ---------------->|-- Renderiza PDF padrão FEBRABAN
   |<-- PDF para impressão --------|  
```

### 9.5 Gerar Boletos em Lote (Final de Mês)

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Boletos em Lote" -->|
   |                               |-- Lista clientes com saldo devedor
   |-- Filtra: saldo mínimo R$X -->|   e CPF/CNPJ cadastrado
   |-- Define vencimento padrão -->|
   |-- Seleciona clientes -------->|
   |-- Confirma geração ---------->|-- Para cada cliente:
   |                               |   - Agrupa fiados pendentes
   |                               |   - Gera 1 boleto consolidado
   |                               |   - Status = GERADO
   |                               |-- Registra auditoria
   |<-- "X boletos gerados" -------|  
   |-- [Gerar Remessa] ----------->|-- Gera arquivo CNAB 400 (.REM)
   |                               |-- Salva registro do arquivo
   |<-- Download do arquivo .REM --|  
   |                               |
   |(usuário envia arquivo ao Bradesco via internet banking)
```

### 9.6 Processar Retorno Bancário (Baixa Automática)

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Importar Retorno" ->|
   |-- Seleciona arquivo .RET ---->|-- Valida formato CNAB 400
   |                               |-- Para cada registro do arquivo:
   |                               |   - Identifica boleto pelo nosso_numero
   |                               |   - Se pago:
   |                               |     → Atualiza boleto: PAGO, data, valor
   |                               |     → Baixa contas a receber vinculadas
   |                               |     → Atualiza saldo do cliente
   |                               |     → Registra pagamento (forma: BOLETO)
   |                               |   - Se rejeitado/cancelado:
   |                               |     → Atualiza status + motivo
   |                               |-- Salva registro do arquivo
   |                               |-- Registra auditoria
   |<-- Resumo: X pagos, Y rejeitados, Z sem alteração |
```

---

## 10. Integração Bradesco — Detalhamento Técnico

### 10.1 Dados da Conta (configuráveis em M13)

| Parâmetro | Descrição | Exemplo |
|---|---|---|
| `bradesco_agencia` | Número da agência (com dígito) | 1234-5 |
| `bradesco_conta` | Número da conta corrente (com dígito) | 123456-7 |
| `bradesco_carteira` | Carteira de cobrança | 09 (sem registro) ou 06 (com registro) |
| `bradesco_cedente_codigo` | Código do cedente/beneficiário | Fornecido pelo banco |
| `bradesco_cedente_nome` | Razão social / nome do beneficiário | Nome da padaria |
| `bradesco_cedente_cnpj` | CNPJ do beneficiário | CNPJ da padaria |
| `bradesco_juros_mes` | Juros ao mês (%) | 1.00 |
| `bradesco_multa_atraso` | Multa por atraso (%) | 2.00 |
| `bradesco_dias_protesto` | Dias para protestar (0 = não protestar) | 0 |
| `bradesco_instrucao_1` | Instrução padrão no boleto | "Não receber após 30 dias do vencimento" |
| `bradesco_instrucao_2` | Segunda instrução | "Cobrar multa de 2% após vencimento" |
| `bradesco_nosso_numero_seq` | Último nosso_numero utilizado (sequencial) | 00000001 |

### 10.2 Formato CNAB 400 — Remessa

| Registro | Posições-chave | Descrição |
|---|---|---|
| **Header** (tipo 0) | Código do cedente, data geração, sequencial | Identificação do arquivo |
| **Detalhe** (tipo 1) | Nosso número, sacado (CPF/nome/endereço), valor, vencimento, instruções | Um por boleto |
| **Trailer** (tipo 9) | Qtd registros, valor total | Fechamento do arquivo |

### 10.3 Formato CNAB 400 — Retorno

| Ocorrência | Código | Ação no Sistema |
|---|---|---|
| Entrada confirmada | 02 | Status → REGISTRADO |
| Liquidação normal | 06 | Status → PAGO, baixa automática nas contas |
| Liquidação em cartório | 15 | Status → PAGO |
| Título não encontrado | 10 | Log de erro, alerta ao admin |
| Entrada rejeitada | 03 | Status → mantém GERADO, registra motivo |
| Baixa solicitada | 09/10 | Status → CANCELADO |

### 10.4 Cálculo do Nosso Número (Bradesco)

O nosso número Bradesco (carteira 09) tem 11 dígitos + 1 dígito verificador:
- Formato: `CCCNNNNNNNNNNN-D` onde:
  - `CCC` = carteira (ex.: 009)
  - `NNNNNNNNNNN` = sequencial (11 posições com zeros à esquerda)
  - `D` = dígito verificador (módulo 11, pesos 2..7)

---

## 11. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-FIN-01 | Dashboard financeiro: cards com "a pagar esta semana", "a receber", "saldo projetado", "fiados em atraso" |
| UI-FIN-02 | Lista de contas a pagar: tabela com vencimento colorido (vermelho: vencido, amarelo: próximo, verde: ok) |
| UI-FIN-03 | Tela de fiado: busca de cliente com saldo, lista de débitos, botão "Receber pagamento" |
| UI-FIN-04 | Fiado por cliente: visão de "extrato" com entradas (compras) e saídas (pagamentos) |
| UI-FIN-05 | Fluxo de caixa: gráfico de barras por dia/semana com entradas (verde) e saídas (vermelho) |
| UI-FIN-06 | DRE: tabela simplificada com possibilidade de detalhar cada linha |
| UI-FIN-07 | Análise de delivery: tabela por plataforma com receita, taxas, custo, lucro |
| UI-FIN-08 | Tela de boletos: lista com nosso_numero, cliente, valor, vencimento, status (cor) — filtros por status e período |
| UI-FIN-09 | Geração de boleto: formulário com seleção de fiados, valor total calculado, campo de vencimento |
| UI-FIN-10 | Geração em lote: lista de clientes com checkbox, saldo, filtro de valor mínimo, botão "Gerar Todos" |
| UI-FIN-11 | Visualização/impressão de boleto: layout padrão FEBRABAN com logo Bradesco, código de barras, linha digitável |
| UI-FIN-12 | Importação de retorno: drag-and-drop do arquivo .RET com resumo de processamento (pagos/rejeitados/sem alteração) |
| UI-FIN-13 | Histórico de remessas/retornos: lista de arquivos processados com data, qtd boletos, valor total |
