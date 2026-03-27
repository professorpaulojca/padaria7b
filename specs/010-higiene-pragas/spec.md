# SPEC-010 — Higiene, Limpeza e Controle de Pragas

> **Módulo**: M10 – Higiene, Limpeza e Controle de Pragas  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Manter registros sanitários, checklists de limpeza periódicos, controle de pragas (dedetização), registro de temperatura de equipamentos e conformidade básica com APPCC (Análise de Perigos e Pontos Críticos de Controle), criando evidências para fiscalizações da vigilância sanitária.

### Evidências

- **RF16**: "Registrar tarefas/ocorrências de manutenção: aferição de balanças, afiação de corta-frios, troca de filtro."
- **RF17**: "Registrar rotinas de higiene e limpeza: mesas, salão, banheiro, cozinha, descarte de lixo."
- **HIG-01 a HIG-06**: Perguntas de operabilidade sobre higiene e limpeza.
- **Quadro de Problemas (Mesas/Limpeza)**: "Consumo no local gera lixo e exige limpeza sem controle de rotina."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-HIG-01** | Checklists de limpeza por área e período: salão, mesas, banheiro, cozinha, produção, calçada | RF17, HIG-01 |
| **RF-HIG-02** | Registro de execução do checklist: quem fez, horário, observações, conformidade (OK/pendência) | RF17, HIG-02 |
| **RF-HIG-03** | Controle de pragas: registro de dedetizações (data, empresa, tipo tratamento, áreas, validade) | Sanitário |
| **RF-HIG-04** | Alerta de vencimento de dedetização (antecedência configurável em M13) | Sanitário |
| **RF-HIG-05** | Registro de inspeção sanitária: data, inspetor, resultado, não-conformidades, prazo de correção | Sanitário |
| **RF-HIG-06** | Checklist de boas práticas (APPCC básico): temperatura equipamentos, higiene pessoal, armazenamento | Boas práticas |
| **RF-HIG-07** | Registro de temperatura de equipamentos: geladeiras, freezers, estufas (manual, com horário) | APPCC |
| **RF-HIG-08** | Controle de materiais de limpeza: estoque mínimo integrado com M03 | HIG-06 |
| **RF-HIG-09** | Alerta de rotinas de limpeza não executadas no prazo | HIG-01 |
| **RF-HIG-10** | Relatório de conformidade sanitária por período | Fiscalização |

---

## 3. Atores

| Ator | Papel |
|---|---|
| **Dono / Gerente** | Configura checklists, define responsáveis, consulta relatórios, registra dedetizações e inspeções. |
| **Administrativo** | Configura e consulta. Registra dedetizações. |
| **Atendente / Funcionário designado** | Executa e registra checklists de limpeza e temperatura. |
| **Demais** | Sem acesso direto. |

---

## 4. Entidades e Dados Conceituais

### 4.1 Modelo de Checklist

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Ex.: "Limpeza Salão - Manhã", "Temperatura Geladeiras" |
| `area` | enum | Sim | `SALAO`, `MESAS`, `BANHEIRO`, `COZINHA`, `PRODUCAO`, `CALCADA`, `EQUIPAMENTOS`, `GERAL` |
| `periodicidade` | enum | Sim | `DIARIO_MANHA`, `DIARIO_TARDE`, `DIARIO_NOITE`, `SEMANAL`, `QUINZENAL`, `MENSAL` |
| `horario_limite` | hora | Não | Horário limite para execução (para gerar alerta) |
| `ativo` | booleano | Sim | Se está ativo |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.2 Item do Checklist (template)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `checklist_id` | referência | Sim | Checklist pai |
| `descricao` | texto | Sim | Ex.: "Limpar mesas", "Varrer salão", "Verificar temp. geladeira 1" |
| `tipo_resposta` | enum | Sim | `SIM_NAO`, `CONFORME_NAO_CONFORME`, `TEMPERATURA`, `TEXTO_LIVRE` |
| `valor_minimo` | numérico | Não | Para tipo TEMPERATURA: mínimo aceitável (ex.: 0°C) |
| `valor_maximo` | numérico | Não | Para tipo TEMPERATURA: máximo aceitável (ex.: 5°C) |
| `ordem` | inteiro | Sim | Ordem de exibição |

### 4.3 Execução de Checklist

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `checklist_id` | referência | Sim | Modelo de checklist executado |
| `data` | data | Sim | Data da execução |
| `hora_execucao` | hora | Sim | Hora em que foi executado |
| `executado_por` | referência | Sim | Quem executou (FK → usuario) |
| `status` | enum | Sim | `CONFORME`, `NAO_CONFORME`, `PARCIAL` |
| `observacao_geral` | texto | Não | Observações gerais |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.4 Resposta de Item do Checklist

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `execucao_id` | referência | Sim | Execução pai |
| `item_id` | referência | Sim | Item do checklist |
| `resposta` | texto | Sim | Valor da resposta (SIM/NAO, CONFORME/NAO_CONFORME, número, texto) |
| `conforme` | booleano | Sim | Se o item está conforme |
| `observacao` | texto | Não | Observação do item |

### 4.5 Dedetização (Controle de Pragas)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `data_execucao` | data | Sim | Data da dedetização |
| `empresa` | texto | Sim | Empresa responsável |
| `cnpj_empresa` | texto | Não | CNPJ da empresa |
| `tipo_tratamento` | texto | Sim | Ex.: "Dedetização geral", "Desratização", "Descupinização" |
| `areas_tratadas` | texto | Sim | Áreas tratadas (ex.: "Cozinha, produção, depósito, salão") |
| `produtos_utilizados` | texto | Não | Produtos químicos usados (para registro) |
| `data_validade` | data | Sim | Validade do serviço (ex.: 6 meses) |
| `certificado` | texto/path | Não | Número ou upload do certificado |
| `custo` | monetário | Não | Custo do serviço |
| `observacao` | texto | Não | Observações |
| `registrado_por` | referência | Sim | Quem registrou |
| `criado_em` | timestamp | Sim | Auditoria |

### 4.6 Inspeção Sanitária

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `data` | data | Sim | Data da inspeção |
| `inspetor` | texto | Sim | Nome do inspetor / fiscal |
| `orgao` | texto | Sim | Órgão (ex.: "Vigilância Sanitária Municipal") |
| `resultado` | enum | Sim | `APROVADO`, `APROVADO_COM_RESSALVAS`, `REPROVADO` |
| `nao_conformidades` | texto | Não | Detalhamento das não-conformidades |
| `prazo_correcao` | data | Não | Data limite para correção |
| `acoes_corretivas` | texto | Não | Ações tomadas |
| `data_resolucao` | data | Não | Data em que as correções foram feitas |
| `registrado_por` | referência | Sim | Quem registrou |
| `criado_em` | timestamp | Sim | Auditoria |

---

## 5. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-HIG-01** | Checklist não executado até o horário limite gera alerta automático para Dono/Gerente. |
| **RN-HIG-02** | Temperatura fora da faixa (min/max) marca item como NÃO CONFORME automaticamente. |
| **RN-HIG-03** | Execução de checklist com qualquer item NÃO CONFORME: status geral = `NAO_CONFORME` ou `PARCIAL`. |
| **RN-HIG-04** | Alerta de dedetização: quando `data_validade - hoje ≤ dias_alerta` (M13), notificar Dono/Gerente. |
| **RN-HIG-05** | Materiais de limpeza com estoque abaixo do mínimo: alerta integrado com M03 (classificados como MATERIAL_LIMPEZA). |
| **RN-HIG-06** | Relatório de conformidade mostra: % de checklists conformes no período, temperaturas fora da faixa, dedetizações em dia. |
| **RN-HIG-07** | Inspeção com resultado REPROVADO gera alerta permanente até que `data_resolucao` seja preenchida. |
| **RN-HIG-08** | Custo de dedetização gera despesa em M08 (categoria: LIMPEZA). |
| **RN-HIG-09** | Todo registro gera log de auditoria (M00). |

---

## 6. Fluxos Principais

### 6.1 Executar Checklist de Limpeza

```
Funcionário                      Sistema
   |                               |
   |-- Acessa "Checklists" ------->|-- Lista checklists pendentes do turno
   |-- Seleciona checklist ------->|-- Exibe itens
   |-- Para cada item: ----------->|
   |   marca SIM/NAO ou           |
   |   informa temperatura        |
   |-- Adiciona observação ------->|
   |-- Confirma ------------------>|-- Calcula conformidade
   |                               |-- Se temperatura fora da faixa: alerta
   |                               |-- Salva execução
   |                               |-- Registra auditoria
   |<-- "Checklist registrado" ----|
```

### 6.2 Registrar Dedetização

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Nova Dedetização" ->|
   |-- Preenche: data, empresa, -->|
   |   tipo, áreas, validade,      |
   |   custo, certificado          |
   |-- Salva ---------------------->|-- Registra dedetização
   |                               |-- Agenda alerta para vencimento
   |                               |-- Gera despesa em M08
   |                               |-- Registra auditoria
   |<-- "Dedetização registrada" --|
```

### 6.3 Verificação Diária Automática

```
Sistema (job diário)
   |
   |-- Verifica checklists do dia não executados:
   |     Para cada: gera alerta "Checklist [X] não executado"
   |-- Verifica dedetizações próximas do vencimento:
   |     Para cada: gera alerta "Dedetização vence em X dias"
   |-- Verifica inspeções com pendência de correção:
   |     Para cada: gera alerta "Correção pendente desde [data]"
   |-- Envia notificações (M14)
```

---

## 7. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-HIG-01 | Dashboard de higiene: status dos checklists do dia (executado/pendente), próxima dedetização, alertas |
| UI-HIG-02 | Checklist: formato de formulário simples com toggle SIM/NÃO ou campo de temperatura com validação |
| UI-HIG-03 | Calendário de dedetizações: visualização de quando foi feita e quando vence |
| UI-HIG-04 | Relatório de conformidade: gráfico de pizza (% conforme) + tabela de não-conformidades |
| UI-HIG-05 | Alerta visual permanente se há inspeção REPROVADA sem resolução |

---

## 8. Seed Data (Checklists Padrão)

| Checklist | Área | Periodicidade | Itens Exemplo |
|---|---|---|---|
| Limpeza Salão - Manhã | SALAO | DIARIO_MANHA | Varrer, passar pano, organizar mesas, limpar vitrine |
| Limpeza Banheiro | BANHEIRO | DIARIO_MANHA + TARDE | Lavar, repor papel, repor sabonete, verificar lixeira |
| Limpeza Cozinha | COZINHA | DIARIO_NOITE | Lavar bancadas, limpar fogão, higienizar utensílios, lavar piso |
| Temperatura Equipamentos | EQUIPAMENTOS | DIARIO_MANHA + TARDE | Geladeira 1 (0-5°C), Geladeira 2 (0-5°C), Freezer (-18 a -12°C), Estufa (60-65°C) |
| Descarte de Lixo | GERAL | DIARIO_NOITE | Lixo orgânico, lixo reciclável, lixo sanitário |
| Higiene Pessoal | GERAL | DIARIO_MANHA | Uniforme limpo, unhas cortadas, cabelo preso, sem adornos |
