# SPEC-013 — Configurações e Parametrização

> **Módulo**: M13 – Configurações e Parametrização  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: MÉDIA — Mas necessário desde a Fase 1 para parametrizar demais módulos  
> **Fontes**:
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`

---

## 1. Objetivo do Módulo

Centralizar todos os parâmetros configuráveis do sistema em um único local, permitindo que o dono/gerente customize comportamentos sem alterar código. Inclui dados da empresa, parâmetros operacionais de cada módulo, e rotinas de backup.

### Evidências

- **Constitution**: "Todo valor que definir fluxo deve ser parametrizado."
- **RNF12**: "Manutenção acadêmica e expansão futura sem acoplamento excessivo entre módulos."

---

## 2. Requisitos Funcionais

| Código | Descrição | Usado por |
|---|---|---|
| **RF-CFG-01** | Dados da empresa: nome fantasia, razão social, CNPJ, endereço, telefone, e-mail, logo (para impressão/relatórios) | M12, impressões |
| **RF-CFG-02** | Parâmetros de estoque: dias de antecedência para alerta de validade (padrão: 3), percentual de estoque mínimo | M03 |
| **RF-CFG-03** | Parâmetros de caixa: valor padrão de troco na abertura, limite de sangria sem aprovação superior | M07 |
| **RF-CFG-04** | Parâmetros de segurança: timeout de sessão (minutos), max tentativas de login, tamanho mínimo de senha, tamanho do PIN | M00 |
| **RF-CFG-05** | Parâmetros de fiado: limite de crédito padrão para novos clientes, dias para considerar atraso, faixas de alerta (7/15/30 dias) | M08 |
| **RF-CFG-06** | Parâmetros de manutenção: periodicidade padrão por tipo de equipamento (dias) | M11 |
| **RF-CFG-07** | Parâmetros de higiene: horários de checklists obrigatórios, áreas cadastradas, frequência de dedetização | M10 |
| **RF-CFG-08** | Parâmetros de relatórios: período padrão de consulta, logotipo em impressão, formato de data | M12 |
| **RF-CFG-09** | Horário de funcionamento da padaria: abertura e fechamento por dia da semana | M09, M07 |
| **RF-CFG-10** | Taxas de delivery por plataforma: percentual ou valor fixo de taxa para iFood, Rappi, etc. | M08 |
| **RF-CFG-11** | Configuração de backup: frequência (diária/semanal), horário preferencial, notificação de status | RNF06 |
| **RF-CFG-12** | Parâmetros de produção: margem padrão sobre custo da ficha técnica, dias para sugestão de produção | M05 |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Dono** | Acesso total. Configura todos os parâmetros. |
| **Gerente** | Consulta parâmetros. Pode alterar parâmetros operacionais (não sensíveis). |
| **Demais** | Sem acesso direto. Usam os parâmetros indiretamente. |

---

## 4. Estrutura de Configuração

### 4.1 Agrupamento por Categoria

| Categoria | Parâmetros | Tela |
|---|---|---|
| **Empresa** | Nome, CNPJ, endereço, telefone, logo, horário funcionamento | "Dados da Empresa" |
| **Segurança** | Timeout, tentativas login, tamanho senha, tamanho PIN | "Segurança" |
| **Estoque** | Dias alerta validade, estoque mínimo % | "Estoque" |
| **Caixa** | Troco padrão, limite sangria | "Caixa" |
| **Fiado** | Limite crédito, dias atraso, faixas alerta | "Fiado" |
| **Delivery** | Taxas por plataforma | "Delivery" |
| **Produção** | Margem padrão, dias sugestão | "Produção" |
| **Higiene** | Horários checklist, áreas, frequência dedetização | "Higiene" |
| **Manutenção** | Periodicidade por tipo | "Manutenção" |
| **Relatórios** | Período padrão, logo, formato data | "Relatórios" |
| **Backup** | Frequência, horário, notificação | "Backup" |

### 4.2 Dados Conceituais — Parâmetro

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `categoria` | texto | Sim | Agrupamento (empresa, seguranca, estoque, ...) |
| `chave` | texto | Sim | Identificador único do parâmetro (ex.: `estoque.dias_alerta_validade`) |
| `valor` | texto | Sim | Valor armazenado (convertido conforme tipo) |
| `tipo` | enum | Sim | `TEXTO`, `NUMERO`, `BOOLEANO`, `LISTA` |
| `descricao` | texto | Sim | Explicação legível do parâmetro |
| `valor_padrao` | texto | Sim | Valor padrão de fábrica |
| `editavel` | booleano | Sim | Se o dono pode alterar (alguns são fixos do sistema) |
| `atualizado_em` | timestamp | Sim | Última alteração |
| `atualizado_por` | referência | Sim | Quem alterou |

### 4.3 Dados Conceituais — Empresa

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Registro único (sempre 1 — single tenant) |
| `nome_fantasia` | texto | Sim | Nome da padaria |
| `razao_social` | texto | Não | Razão social |
| `cnpj` | texto | Não | CNPJ formatado |
| `endereco` | texto | Não | Endereço completo |
| `telefone` | texto | Não | Telefone principal |
| `email` | texto | Não | E-mail de contato |
| `logo` | binário/path | Não | Logo para impressão |
| `horario_funcionamento` | JSON | Sim | Horários por dia da semana |

---

## 5. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-CFG-01** | Toda alteração de parâmetro gera registro de auditoria (M00): quem alterou, quando, valor anterior, valor novo. |
| **RN-CFG-02** | Parâmetros com `editavel = false` não aparecem para edição na interface (apenas para visualização). |
| **RN-CFG-03** | Alteração de parâmetros de segurança exige confirmação de senha do usuário. |
| **RN-CFG-04** | Valores de parâmetros são validados conforme tipo e faixa aceitável antes de salvar. |
| **RN-CFG-05** | O sistema deve funcionar com valores padrão caso nenhuma configuração tenha sido personalizada (zero-config inicial). |
| **RN-CFG-06** | Logo da empresa aceita formatos PNG/JPG, máximo 2MB. |
| **RN-CFG-07** | Horário de funcionamento usa formato HH:MM em 24h. |

---

## 6. Seed Data (Valores Padrão)

| Chave | Valor Padrão | Tipo |
|---|---|---|
| `seguranca.timeout_minutos` | 30 | NUMERO |
| `seguranca.max_tentativas_login` | 5 | NUMERO |
| `seguranca.tamanho_minimo_senha` | 6 | NUMERO |
| `seguranca.tamanho_pin` | 4 | NUMERO |
| `estoque.dias_alerta_validade` | 3 | NUMERO |
| `caixa.troco_padrao` | 200.00 | NUMERO |
| `caixa.limite_sangria_sem_aprovacao` | 500.00 | NUMERO |
| `fiado.limite_credito_padrao` | 200.00 | NUMERO |
| `fiado.dias_atraso_alerta` | 7 | NUMERO |
| `producao.margem_padrao_percentual` | 50 | NUMERO |
| `producao.dias_historico_sugestao` | 7 | NUMERO |
| `relatorios.periodo_padrao_dias` | 30 | NUMERO |
| `backup.frequencia` | DIARIA | TEXTO |
| `backup.horario` | 23:00 | TEXTO |

---

## 7. Fluxo Principal — Alterar Parâmetro

```
Dono                             Sistema
   |                               |
   |-- Acessa Configurações ------>|
   |-- Seleciona categoria ------->|-- Exibe parâmetros editáveis da categoria
   |-- Altera valor de parâmetro ->|
   |-- Clica "Salvar" ------------>|-- Valida tipo e faixa
   |                               |-- Se segurança: solicita senha
   |-- Confirma senha (se pedido)->|
   |                               |-- Salva novo valor
   |                               |-- Registra auditoria (valor anterior → novo)
   |<-- "Configuração salva" ------|
```

---

## 8. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-CFG-01 | Tela organizada em abas ou seções por categoria (Empresa, Segurança, Estoque, etc.) |
| UI-CFG-02 | Cada parâmetro exibe: nome legível, valor atual, valor padrão, campo de edição |
| UI-CFG-03 | Botão "Restaurar Padrão" individual por parâmetro |
| UI-CFG-04 | Upload de logo com preview antes de salvar |
| UI-CFG-05 | Horário de funcionamento: tabela visual com dias da semana e campos de hora |
