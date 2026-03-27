# SPEC-011 — Manutenção e Equipamentos

> **Módulo**: M11 – Manutenção e Equipamentos  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Cadastrar equipamentos da padaria e manter rotinas de manutenção preventiva (programada) e corretiva (emergencial), evitando paradas inesperadas que afetam produção e atendimento.

### Evidências

- **RF16**: "Registrar tarefas/ocorrências de manutenção: aferição de balanças, afiação de corta-frios, troca de filtro."
- **HIG-03**: "Troca do filtro de água: item de manutenção periódica."
- **HIG-04**: "Aferição das balanças: manutenção programada."
- **HIG-05**: "Cadastro de equipamentos + rotina de manutenção preventiva e corretiva."
- **Quadro de Problemas (Manutenção)**: "Filtro enferrujado, balanças sem aferição, equipamentos sem rotina."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-MAN-01** | Cadastrar equipamentos: descrição, marca, modelo, localização, data aquisição, valor, foto opcional | HIG-05 |
| **RF-MAN-02** | Registrar manutenção preventiva: tipo (aferição, troca filtro, afiação), periodicidade, última execução | RF16, HIG-03/04 |
| **RF-MAN-03** | Registrar manutenção corretiva: equipamento, problema, data, técnico, custo, tempo parado | RF16 |
| **RF-MAN-04** | Alerta de manutenção preventiva pendente (antecedência configurável em M13) | Prevenção |
| **RF-MAN-05** | Histórico de manutenção por equipamento: preventivas e corretivas | Controle |
| **RF-MAN-06** | Controle de vida útil: alertar quando equipamento atinge X anos ou X manutenções corretivas | Gestão |
| **RF-MAN-07** | Relatório de custos de manutenção por período e por equipamento | OE-06 |
| **RF-MAN-08** | Registro de fornecedores/técnicos de manutenção (integração com M02) | Organização |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono / Gerente** | Acesso total. Cadastra equipamentos. Programa preventivas. Aprova custos. |
| **Administrativo** | Registra manutenções preventivas e corretivas. Acompanha alertas. |
| **Demais** | Podem reportar problema em equipamento (simplificado). |

---

## 4. Entidades e Dados Conceituais

### 4.1 Equipamento

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `descricao` | texto | Sim | Ex.: "Forno Industrial 1", "Balança Digital Caixa" |
| `marca` | texto | Não | Marca do equipamento |
| `modelo` | texto | Não | Modelo |
| `numero_serie` | texto | Não | Número de série |
| `localizacao` | texto | Sim | Onde está (ex.: "Cozinha", "Atendimento", "Depósito") |
| `data_aquisicao` | data | Não | Data de compra/aquisição |
| `valor_aquisicao` | monetário | Não | Valor de compra |
| `vida_util_anos` | inteiro | Não | Vida útil estimada (para alerta) |
| `foto` | binário/path | Não | Foto do equipamento |
| `situacao` | enum | Sim | `ATIVO`, `EM_MANUTENCAO`, `INATIVO`, `DESCARTADO` |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.2 Plano de Manutenção Preventiva

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `equipamento_id` | referência | Sim | Equipamento (FK) |
| `tipo` | texto | Sim | Ex.: "Aferição", "Troca de filtro", "Afiação", "Limpeza interna", "Revisão geral" |
| `periodicidade_dias` | inteiro | Sim | A cada quantos dias deve ser realizada |
| `ultima_execucao` | data | Não | Data da última execução |
| `proxima_execucao` | data | Calculado | `ultima_execucao + periodicidade_dias` |
| `responsavel_padrao` | texto | Não | Quem normalmente executa (técnico externo ou interno) |
| `custo_estimado` | monetário | Não | Custo estimado por execução |
| `ativo` | booleano | Sim | Se o plano está ativo |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.3 Registro de Manutenção (execução — preventiva ou corretiva)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `equipamento_id` | referência | Sim | Equipamento (FK) |
| `plano_id` | referência | Não | Plano preventivo (se preventiva, FK) |
| `tipo` | enum | Sim | `PREVENTIVA`, `CORRETIVA` |
| `descricao` | texto | Sim | O que foi feito / problema encontrado |
| `data_execucao` | data | Sim | Data da manutenção |
| `tecnico` | texto | Não | Técnico/empresa que executou |
| `custo` | monetário | Não | Custo da manutenção |
| `tempo_parado_horas` | numérico | Não | Horas que o equipamento ficou parado (para corretiva) |
| `pecas_substituidas` | texto | Não | Peças trocadas |
| `resultado` | enum | Sim | `RESOLVIDO`, `PALIATIVO`, `AGUARDANDO_PECA`, `NAO_RESOLVIDO` |
| `observacao` | texto | Não | Observações adicionais |
| `registrado_por` | referência | Sim | Quem registrou (FK → usuario) |
| `criado_em` | timestamp | Sim | Auditoria |

---

## 5. Ciclo de Vida do Equipamento

```
ATIVO → EM_MANUTENCAO → ATIVO
   ↓                       ↓
INATIVO               DESCARTADO
```

| Estado | Descrição |
|---|---|
| `ATIVO` | Em uso normal. |
| `EM_MANUTENCAO` | Temporariamente fora de uso por manutenção corretiva. |
| `INATIVO` | Retirado de operação mas mantido (reserva). |
| `DESCARTADO` | Descartado/vendido. Dados mantidos para histórico. |

---

## 6. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-MAN-01** | Ao registrar manutenção preventiva, o sistema atualiza `ultima_execucao` no plano e recalcula `proxima_execucao`. |
| **RN-MAN-02** | Alerta de preventiva: quando `proxima_execucao - hoje ≤ dias_alerta` (M13), notificar Dono/Gerente/Admin. |
| **RN-MAN-03** | Manutenção corretiva pode alterar situação do equipamento para `EM_MANUTENCAO` (e de volta para `ATIVO` ao concluir). |
| **RN-MAN-04** | Custo de manutenção gera despesa em M08 (categoria: MANUTENCAO). |
| **RN-MAN-05** | Alerta de vida útil: quando `hoje - data_aquisicao ≥ vida_util_anos × 365`, notificar Dono. |
| **RN-MAN-06** | Alerta de corretivas frequentes: se equipamento teve ≥ 3 corretivas nos últimos 6 meses, notificar Dono. |
| **RN-MAN-07** | Equipamento DESCARTADO mantém todo o histórico de manutenção para consulta. |
| **RN-MAN-08** | Todo registro gera log de auditoria (M00). |

---

## 7. Fluxos Principais

### 7.1 Cadastrar Equipamento com Plano Preventivo

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Novo Equipamento" ->|
   |-- Preenche dados básicos ---->|
   |-- Salva ---------------------->|-- Cria equipamento ATIVO
   |-- Acessa aba "Preventivas" -->|
   |-- Adiciona plano: ----------->|
   |   "Troca de filtro",         |
   |   a cada 180 dias,           |
   |   custo est. R$150           |
   |-- Salva plano --------------->|-- Calcula proxima_execucao
   |                               |-- Registra auditoria
   |<-- "Equipamento cadastrado" --|
```

### 7.2 Registrar Manutenção Corretiva

```
Qualquer usuário                 Dono/Admin                   Sistema
   |                               |                             |
   |-- Reporta: "Forno parou" ---->|                             |
   |                               |-- Acessa equipamento ------>|
   |                               |-- Registra corretiva: ----->|
   |                               |   problema, técnico,        |
   |                               |   custo, tempo parado       |
   |                               |                              |-- Atualiza situação EM_MANUTENCAO
   |                               |                              |-- Registra manutenção
   |                               |                              |-- (após resolver):
   |                               |-- Marca como RESOLVIDO ----->|-- Situação → ATIVO
   |                               |                              |-- Gera despesa M08
   |                               |                              |-- Registra auditoria
   |                               |<-- "Manutenção registrada" --|
```

### 7.3 Verificação de Preventivas (Job Diário)

```
Sistema (automático)
   |
   |-- Consulta planos preventivos ativos
   |-- Para cada:
   |     Se proxima_execucao - hoje ≤ dias_alerta:
   |       → Alerta: "[Equipamento] - [Tipo] vence em X dias"
   |     Se proxima_execucao < hoje:
   |       → Alerta ATRASADO: "[Equipamento] - [Tipo] atrasada X dias"
   |-- Notifica Dono/Gerente
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-MAN-01 | Lista de equipamentos: cards com foto, nome, situação (cor), próxima preventiva |
| UI-MAN-02 | Ficha do equipamento: dados + aba "Planos Preventivos" + aba "Histórico de Manutenção" |
| UI-MAN-03 | Calendário de manutenção: visualização mensal com preventivas programadas |
| UI-MAN-04 | Dashboard de manutenção: cards "preventivas pendentes", "corretivas no mês", "custo total do mês" |
| UI-MAN-05 | Alerta visual permanente se há preventiva atrasada (badge vermelho na barra) |

---

## 9. Seed Data (Equipamentos Típicos de Padaria)

| Equipamento | Preventivas Sugeridas | Periodicidade |
|---|---|---|
| Forno Industrial | Revisão geral, limpeza interna | 90 / 30 dias |
| Balança Digital (caixa) | Aferição | 180 dias |
| Balança (produção) | Aferição | 180 dias |
| Cortador de Frios | Afiação, limpeza | 30 / 7 dias |
| Geladeira Exposição | Revisão compressor, limpeza serpentina | 180 / 30 dias |
| Freezer | Revisão, limpeza serpentina | 180 / 30 dias |
| Filtro de Água | Troca de elemento filtrante | 180 dias |
| Masseira | Lubrificação, revisão geral | 90 / 180 dias |
| Estufa | Revisão resistência, limpeza | 90 / 7 dias |
| Vitrine Refrigerada | Revisão, limpeza | 180 / 7 dias |
