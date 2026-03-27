# SPEC-002 — Cadastro de Pessoas

> **Módulo**: M02 – Cadastro de Pessoas  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: ALTA — Base para fiado, pessoal, compras e atendimento  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Centralizar o cadastro de clientes, funcionários e fornecedores com informações relevantes para cada papel. Este módulo alimenta diretamente o fiado (M08), gestão de pessoal (M09), compras (M04) e atendimento (M06).

### Evidências

- **RF12**: "Manter cadastro de clientes fiado"
- **RF14**: "Manter cadastro de funcionários: função, situação, dados básicos"
- **RF06**: "Cadastrar fornecedores e registrar compras"
- **FIN-01**: "Registro por cliente: nome, contato, data, itens/valor"

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-PES-01** | Cadastrar clientes: nome, apelido, telefone/WhatsApp, endereço simplificado, observações, situação, flag "compra fiado" | RF12, FIN-01 |
| **RF-PES-02** | Cadastrar funcionários: nome, função, data admissão, salário base, telefone, situação (ativo/afastado/desligado), foto opcional | RF14 |
| **RF-PES-03** | Cadastrar fornecedores: razão social, nome fantasia, CNPJ/CPF, telefone, e-mail, endereço, prazo entrega médio, condição de pagamento | RF06 |
| **RF-PES-04** | Vincular fornecedores aos insumos/produtos que fornecem (relação N:N com preço referência) | Compras inteligentes |
| **RF-PES-05** | Classificar fornecedores com avaliação simples (1 a 5 estrelas) | Melhoria de compras |
| **RF-PES-06** | Histórico de interações com fornecedor: última compra, prazo cumprido, problemas | Melhoria de compras |
| **RF-PES-07** | Busca rápida de cliente por nome/apelido/telefone | FIN-01, agilidade |
| **RF-PES-08** | Registrar dependentes/autorizados para fiado (ex.: "esposa do João pode retirar fiado") | Realidade da padaria |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Dono** | Acesso total a todos os cadastros. |
| **Gerente** | Acesso total a todos os cadastros. |
| **Administrativo** | Cadastra/edita fornecedores. Consulta clientes e funcionários. |
| **Caixa** | Consulta clientes (para fiado). Sem acesso a fornecedores ou funcionários. |
| **Atendente** | Consulta clientes (para identificação). |

---

## 4. Entidades e Dados Conceituais

### 4.1 Cliente

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome completo |
| `apelido` | texto | Não | Como é conhecido na padaria (ex.: "Seu Zé", "Dona Maria") |
| `telefone` | texto | Não | Telefone principal / WhatsApp |
| `endereco` | texto | Não | Endereço simplificado (rua, número, bairro) |
| `observacoes` | texto | Não | Informações relevantes (ex.: "sempre paga sexta") |
| `compra_fiado` | booleano | Sim | Se está autorizado a comprar fiado (padrão: false) |
| `limite_credito` | monetário | Não | Limite individual de fiado (se null, usa parâmetro global de M13) |
| `situacao` | enum | Sim | `ATIVO`, `INATIVO` |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.2 Autorizado Fiado (Dependente)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `cliente_id` | referência | Sim | Cliente titular do fiado |
| `nome` | texto | Sim | Nome do autorizado |
| `parentesco` | texto | Não | Relação (esposa, filho, funcionário, etc.) |
| `ativo` | booleano | Sim | Se ainda está autorizado |

### 4.3 Funcionário

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome completo |
| `funcao` | texto | Sim | Função (padeiro, atendente, caixa, auxiliar, chapeiro, faxineira) |
| `data_admissao` | data | Sim | Data de admissão |
| `data_desligamento` | data | Não | Data de desligamento (se aplicável) |
| `salario_base` | monetário | Sim | Salário base mensal |
| `telefone` | texto | Não | Telefone de contato |
| `foto` | binário/path | Não | Foto para identificação |
| `situacao` | enum | Sim | `ATIVO`, `AFASTADO`, `DESLIGADO` |
| `observacoes` | texto | Não | Informações relevantes |
| `usuario_id` | referência | Não | Vínculo com usuário do sistema (M00), se aplicável |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.4 Fornecedor

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `razao_social` | texto | Não | Razão social |
| `nome_fantasia` | texto | Sim | Nome de uso comum (ex.: "Distribuidora Silva") |
| `cnpj_cpf` | texto | Não | CNPJ ou CPF |
| `telefone` | texto | Sim | Telefone principal |
| `email` | texto | Não | E-mail de contato |
| `endereco` | texto | Não | Endereço |
| `contato_nome` | texto | Não | Nome da pessoa de contato |
| `prazo_entrega_dias` | inteiro | Não | Prazo médio de entrega em dias |
| `condicao_pagamento` | texto | Não | Ex.: "À vista", "30 dias", "Boleto 15 dias" |
| `avaliacao` | inteiro | Não | 1 a 5 estrelas (avaliação subjetiva do dono) |
| `situacao` | enum | Sim | `ATIVO`, `INATIVO` |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### 4.5 Fornecedor × Insumo (vínculo)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `fornecedor_id` | referência | Sim | Fornecedor |
| `insumo_id` | referência | Sim | Insumo que fornece |
| `preco_referencia` | monetário | Não | Último preço praticado |
| `data_referencia` | data | Não | Data do último preço informado |
| `observacao` | texto | Não | Ex.: "Entrega só em dia útil" |

### 4.6 Interação com Fornecedor (Histórico)

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `fornecedor_id` | referência | Sim | Fornecedor |
| `tipo` | enum | Sim | `COMPRA`, `PROBLEMA`, `CONTATO`, `OBSERVACAO` |
| `descricao` | texto | Sim | Detalhamento |
| `data` | timestamp | Sim | Quando ocorreu |
| `registrado_por` | referência | Sim | Usuário que registrou |

---

## 5. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-PES-01** | Cliente com fiado em aberto (saldo devedor > 0) não pode ser inativado. Regularizar primeiro. |
| **RN-PES-02** | Funcionário desligado mantém todos os registros históricos (ponto, vales, ocorrências) para consulta. |
| **RN-PES-03** | Fornecedor com pedido de compra em aberto não pode ser inativado. |
| **RN-PES-04** | O campo `compra_fiado` do cliente só pode ser ativado pelo Dono, Gerente ou Caixa autorizado. |
| **RN-PES-05** | Autorizado/dependente de fiado consome o limite do cliente titular. |
| **RN-PES-06** | Se cliente atinge o limite de crédito, o sistema bloqueia novo fiado (exige aprovação do Dono/Gerente para exceção). |
| **RN-PES-07** | CNPJ/CPF de fornecedor, se informado, deve ser único entre fornecedores ativos. |
| **RN-PES-08** | Avaliação de fornecedor (1-5) é opcional e subjetiva — atualizada manualmente pelo dono/gerente. |
| **RN-PES-09** | Ao vincular fornecedor a insumo com preço, o preço de referência é informativo (não vinculante para pedido de compra). |
| **RN-PES-10** | Toda alteração de cadastro gera log de auditoria (M00). |

---

## 6. Fluxos Principais

### 6.1 Cadastrar Cliente

```
Dono/Caixa                       Sistema
   |                               |
   |-- Acessa "Novo Cliente" ----->|
   |-- Preenche: nome, apelido, -->|
   |   telefone, endereço,         |
   |   compra_fiado (S/N),         |
   |   limite_credito (se fiado)   |
   |-- Clica "Salvar" ------------>|-- Valida campos obrigatórios
   |                               |-- Se compra_fiado=S: verifica permissão do usuário
   |                               |-- Salva com situacao=ATIVO
   |                               |-- Registra auditoria
   |<-- "Cliente cadastrado" ------|
```

### 6.2 Cadastrar Fornecedor com Vínculo de Insumos

```
Dono/Admin                       Sistema
   |                               |
   |-- Acessa "Novo Fornecedor" -->|
   |-- Preenche dados básicos ---->|
   |-- Clica "Salvar" ------------>|-- Salva fornecedor
   |-- Acessa aba "Insumos" ------>|
   |-- Vincula insumos: ---------->|
   |   trigo, sal, fermento        |
   |   com preço referência        |
   |-- Salva vínculos ------------>|-- Registra fornecedor_insumo
   |                               |-- Registra auditoria
   |<-- "Fornecedor cadastrado" ---|
```

### 6.3 Busca Rápida de Cliente (para Fiado)

```
Caixa                            Sistema
   |                               |
   |-- Digita nome/apelido/ ------>|
   |   telefone no campo de busca  |
   |                               |-- Busca instantânea (like + indexado)
   |                               |-- Retorna lista com: nome, apelido,
   |                               |   saldo fiado, situação, flag compra_fiado
   |<-- Exibe resultados ----------|
   |-- Seleciona cliente --------->|-- Carrega dados para operação de fiado
```

---

## 7. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-PES-01 | Abas separadas: Clientes / Funcionários / Fornecedores |
| UI-PES-02 | Lista de clientes com indicador visual de quem compra fiado (ícone/cor) e saldo devedor |
| UI-PES-03 | Lista de funcionários com situação colorida: verde (ativo), amarelo (afastado), cinza (desligado) |
| UI-PES-04 | Ficha do fornecedor: dados + aba "Insumos fornecidos" + aba "Histórico de interações" + avaliação em estrelas |
| UI-PES-05 | Busca rápida de cliente: campo de autocomplete na tela de fiado (M08) e na tela de pedido (M06) |
| UI-PES-06 | No cadastro de cliente: seção "Autorizados" para adicionar/remover dependentes de fiado |
