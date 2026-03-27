# SPEC-009 — Controle de Pessoal

> **Módulo**: M09 – Controle de Pessoal  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Gestão simplificada de funcionários: registro de ponto, escalas de trabalho, controle de vales, registro de ocorrências e resumo mensal. Não é folha de pagamento completa — é controle operacional para evitar pagamentos incorretos e conflitos.

### Evidências

- **RF14**: "Manter cadastro de funcionários: função, situação, dados básicos."
- **RF15**: "Registrar horários de entrada e saída dos funcionários, inclusive correções justificadas."
- **RF13**: "Registrar retirada de vale para funcionário: data, valor, observação."
- **Quadro de Problemas (Funcionários)**: "Ponto pouco confiável, horas mal controladas, vales sem anotação."
- **FIN-04**: "Registrar no momento da retirada: valor, funcionário, data, motivo, usuário que lançou."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-RH-01** | Registro de ponto simplificado: entrada e saída, com horário e dispositivo | RF15 |
| **RF-RH-02** | Correção de ponto com justificativa obrigatória e aprovação do dono/gerente | RF15 |
| **RF-RH-03** | Registro de vale: valor, data, motivo, quem autorizou, vinculação ao caixa (M07) | RF13, FIN-04 |
| **RF-RH-04** | Resumo mensal por funcionário: dias trabalhados, horas totais, atrasos, vales acumulados | OE-04 |
| **RF-RH-05** | Cadastro de escalas de trabalho por funcionário (turno, dias da semana, horário esperado) | Controle |
| **RF-RH-06** | Alerta de irregularidades: falta sem justificativa, atraso frequente, excesso de vales | Controle |
| **RF-RH-07** | Registro de ocorrências: advertência verbal, advertência escrita, elogio, observação | Gestão |
| **RF-RH-08** | Histórico completo do funcionário: admissão, ponto, vales, ocorrências, alterações salariais | OE-04 |
| **RF-RH-09** | Relatório de custo de pessoal: salários + vales por período | OE-06 |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono / Gerente** | Acesso total. Define escalas. Aprova correções de ponto. Registra ocorrências. |
| **Funcionário (via login próprio)** | Registra próprio ponto (entrada/saída). Consulta próprios vales e horas. |
| **Caixa** | Registra vale de funcionário (saída do caixa → M07). |

---

## 4. Entidades e Dados Conceituais

### 4.1 Registro de Ponto

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `funcionario_id` | referência | Sim | Funcionário (FK → M02) |
| `data` | data | Sim | Data do ponto |
| `hora_entrada` | hora | Sim | Hora de entrada registrada |
| `hora_saida` | hora | Não | Hora de saída (null se ainda não saiu) |
| `horas_trabalhadas` | numérico | Calculado | `hora_saida - hora_entrada` (descontado intervalo se houver) |
| `tipo` | enum | Sim | `NORMAL`, `CORRECAO`, `FALTA_JUSTIFICADA`, `FALTA_NAO_JUSTIFICADA` |
| `corrigido` | booleano | Sim | Se houve correção manual |
| `justificativa_correcao` | texto | Condicional | Obrigatório se corrigido = true |
| `aprovado_por` | referência | Condicional | Quem aprovou a correção (FK → usuario) |
| `registrado_por` | referência | Sim | Quem registrou (pode ser o próprio funcionário) |
| `dispositivo` | texto | Não | Identificação do dispositivo usado para registro |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.2 Escala de Trabalho

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `funcionario_id` | referência | Sim | Funcionário |
| `dia_semana` | enum | Sim | `SEG`, `TER`, `QUA`, `QUI`, `SEX`, `SAB`, `DOM` |
| `hora_entrada_esperada` | hora | Sim | Horário esperado de entrada |
| `hora_saida_esperada` | hora | Sim | Horário esperado de saída |
| `folga` | booleano | Sim | Se é dia de folga |

### 4.3 Vale de Funcionário

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `funcionario_id` | referência | Sim | Funcionário (FK → M02) |
| `valor` | monetário | Sim | Valor do vale |
| `data` | data | Sim | Data de retirada |
| `motivo` | texto | Sim | Motivo da solicitação |
| `autorizado_por` | referência | Sim | Quem autorizou (Dono/Gerente) |
| `registrado_por` | referência | Sim | Quem lançou no sistema (pode ser o caixa) |
| `movimento_caixa_id` | referência | Não | Movimento de caixa vinculado (M07) se saiu do caixa |
| `mes_referencia` | texto | Sim | Mês para desconto (ex.: "2026-03") |
| `descontado` | booleano | Sim | Se já foi descontado do salário |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.4 Ocorrência

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `funcionario_id` | referência | Sim | Funcionário |
| `tipo` | enum | Sim | `ADVERTENCIA_VERBAL`, `ADVERTENCIA_ESCRITA`, `ELOGIO`, `OBSERVACAO`, `SUSPENSAO` |
| `descricao` | texto | Sim | Detalhamento da ocorrência |
| `data` | data | Sim | Data da ocorrência |
| `registrado_por` | referência | Sim | Quem registrou |
| `criado_em` | timestamp | Sim | Auditoria |

---

## 5. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-RH-01** | Ponto só pode ser registrado por funcionário ATIVO com login no sistema. |
| **RN-RH-02** | Registro de ponto usa horário do servidor (não do dispositivo) para evitar manipulação. |
| **RN-RH-03** | Correção de ponto exige justificativa e aprovação do Dono/Gerente. Antes da aprovação, o ponto original é mantido. |
| **RN-RH-04** | Atraso = entrada real > entrada esperada (escala) + tolerância configurável (padrão: 10 min). |
| **RN-RH-05** | Falta não justificada = dia útil da escala sem registro de ponto e sem justificativa. |
| **RN-RH-06** | Vale exige autorização do Dono ou Gerente. Caixa pode registrar mas precisa de autorização registrada. |
| **RN-RH-07** | Soma de vales no mês não pode exceder o salário base do funcionário (alerta ao atingir 80%). |
| **RN-RH-08** | Vale gera saída financeira em M08 (categoria: despesa de pessoal) e movimento de caixa em M07 (se saiu do caixa). |
| **RN-RH-09** | Ocorrências são somente do Dono/Gerente. Funcionário pode visualizar as próprias mas não criar nem editar. |
| **RN-RH-10** | Todo registro gera log de auditoria (M00). |

---

## 6. Fluxos Principais

### 6.1 Registro de Ponto

```
Funcionário                      Sistema
   |                               |
   |-- Faz login ----------------->|
   |-- Clica "Registrar Ponto" --->|
   |                               |-- Verifica se já há entrada sem saída hoje:
   |                               |   Se não: registra ENTRADA
   |                               |   Se sim: registra SAÍDA
   |                               |-- Compara com escala → detecta atraso?
   |                               |-- Registra dispositivo + horário servidor
   |                               |-- Registra auditoria
   |<-- "Entrada registrada -------|
   |     às 06:03. Bom turno!" ----|
```

### 6.2 Registro de Vale

```
Funcionário                      Caixa                         Sistema
   |                               |                             |
   |-- Solicita vale R$100 ------->|                             |
   |                               |-- Acessa "Registrar Vale" ->|
   |                               |-- Seleciona funcionário ---->|
   |                               |-- Informa valor + motivo --->|
   |                               |                              |-- Verifica limite (80% salário)
   |                               |                              |-- Se ok:
Dono/Gerente                       |                              |
   |-- Autoriza (senha/PIN) ------>|                              |
   |                               |                              |-- Registra vale
   |                               |                              |-- Gera movimento caixa (M07)
   |                               |                              |-- Gera saída financeira (M08)
   |                               |                              |-- Registra auditoria
   |                               |<-- "Vale registrado" --------|
```

### 6.3 Resumo Mensal

```
Dono/Gerente                     Sistema
   |                               |
   |-- Acessa "Resumo Mensal" ---->|
   |-- Seleciona mês + func. ----->|-- Calcula:
   |                               |   - Dias trabalhados
   |                               |   - Horas totais
   |                               |   - Atrasos (qtd + total min)
   |                               |   - Faltas (justificadas + não)
   |                               |   - Vales acumulados
   |                               |   - Salário base - vales = líquido estimado
   |<-- Exibe resumo ---------------|
   |-- [Imprime] ----------------->|
```

---

## 7. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-RH-01 | Tela de ponto: botão grande "Registrar Ponto" — muda texto dinamicamente (Entrada/Saída) |
| UI-RH-02 | Espelho de ponto: calendário mensal com dia a dia, horas, atrasos, faltas — em formato de tabela |
| UI-RH-03 | Lista de vales: tabela com funcionário, valor, data, motivo, status de desconto |
| UI-RH-04 | Escalas: tabela visual (grade) com dias da semana × funcionários |
| UI-RH-05 | Alerta visual quando funcionário atinge 80% do salário em vales |
| UI-RH-06 | Timer de correção de ponto: workflow com justificativa → aprovação → efetivação |
