# SPEC-000 — Segurança e Acesso

> **Módulo**: M00 – Segurança e Acesso  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: CRÍTICA — Pré-requisito para todos os demais módulos  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Garantir que cada pessoa acesse somente as funcionalidades e informações que seu perfil permite, com autenticação segura, controle de sessão e rastreabilidade total de ações. O módulo permeia todo o sistema — nenhuma tela ou operação funciona sem passar por ele.

### Evidências

- **RNF03**: "Autenticação por usuário e senha, com perfis de acesso por função."
- **RNF04**: "Operações relevantes registradas para auditoria básica: data, hora, usuário responsável."
- **INF-06**: "Perfis: atendente, caixa, dono/gerente, cozinha/produção."
- **PES-01**: "Sistema objetivo, poucas opções por vez."

---

## 2. Requisitos Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-SEC-01** | Autenticação por usuário e senha com hash seguro (bcrypt) | RNF03 |
| **RF-SEC-02** | Gerenciamento de perfis de acesso: Dono, Gerente, Caixa, Atendente, Chapeiro/Cozinha, Administrativo | RNF03, INF-06 |
| **RF-SEC-03** | Atribuição de permissões por perfil: leitura, escrita, exclusão, aprovação — por módulo/funcionalidade | RNF03 |
| **RF-SEC-04** | Registro de log de auditoria: toda ação relevante grava usuário, data/hora, ação, entidade afetada | RNF04 |
| **RF-SEC-05** | Controle de sessão: timeout por inatividade configurável, logout automático | Segurança |
| **RF-SEC-06** | Bloqueio de conta após N tentativas de login incorretas (N configurável em M13) | Segurança |
| **RF-SEC-07** | Troca de senha obrigatória no primeiro acesso | Segurança |
| **RF-SEC-08** | Recuperação/reset de senha pelo administrador (dono/gerente) | PES-01 (simplicidade) |
| **RF-SEC-09** | Dashboard de sessões ativas (para dono/gerente verificar quem está logado) | Controle |
| **RF-SEC-10** | Modo "troca rápida de usuário" via PIN de 4-6 dígitos para dispositivos compartilhados | INF-02, PES-01 |

### Requisitos Não Funcionais Aplicáveis

| RNF | Relevância |
|---|---|
| **RNF01** | Login deve ser simples: poucos campos, botões claros |
| **RNF02** | Utilizável por pessoas com pouca familiaridade digital |
| **RNF03** | Perfis de acesso por função |
| **RNF04** | Auditoria básica: data, hora, usuário |
| **RNF09** | Mensagens de erro compreensíveis (ex.: "Senha incorreta", não "Error 401") |

---

## 3. Atores

| Ator | Papel neste módulo |
|---|---|
| **Dono** | Acesso total. Cria/edita usuários, perfis, permissões. Reseta senhas. Vê auditoria. |
| **Gerente** | Acesso amplo. Consulta auditoria. Não cria perfis novos nem altera configurações sensíveis. |
| **Caixa** | Faz login/logout. Troca rápida. Sem acesso a gestão de usuários. |
| **Atendente** | Faz login/logout. Troca rápida. Sem acesso a gestão de usuários. |
| **Chapeiro/Cozinha** | Faz login simples (pode ser via PIN). Acesso restrito à fila de preparo. |
| **Administrativo** | Faz login. Acesso a estoque, compras, relatórios operacionais. |

---

## 4. Perfis e Matriz de Permissões

### 4.1 Perfis Padrão

| Perfil | Descrição | Pode ser alterado? |
|---|---|---|
| `DONO` | Acesso irrestrito. Super-administrador. | Não — perfil fixo do sistema |
| `GERENTE` | Acesso amplo exceto gestão de usuários e config sensíveis | Sim — permissões ajustáveis |
| `CAIXA` | Caixa, pagamentos, fiado, consulta de preços | Sim |
| `ATENDENTE` | Pedidos, comandas, consulta de cardápio | Sim |
| `CHAPEIRO` | Fila de preparo (leitura + atualização de status) | Sim |
| `ADMINISTRATIVO` | Estoque, compras, fornecedores, relatórios operacionais | Sim |

### 4.2 Matriz de Acesso por Módulo (padrão inicial)

| Módulo | DONO | GERENTE | CAIXA | ATENDENTE | CHAPEIRO | ADMIN |
|---|---|---|---|---|---|---|
| M00 Segurança | Total | Consulta | — | — | — | — |
| M01 Cadastros | Total | Total | Leitura | Leitura | — | Total |
| M02 Pessoas | Total | Total | Leitura | Leitura | — | Leitura |
| M03 Estoque | Total | Total | Leitura | Leitura | — | Total |
| M04 Compras | Total | Total | — | — | — | Total |
| M05 Produção | Total | Total | — | — | Leitura | Total |
| M06 Pedidos | Total | Total | Leitura | Total | Preparo | Leitura |
| M07 Caixa | Total | Total | Total | — | — | Leitura |
| M08 Financeiro | Total | Total | Parte | — | — | Parte |
| M09 Pessoal | Total | Total | — | — | — | Leitura |
| M10 Higiene | Total | Total | — | Registro | — | Total |
| M11 Manutenção | Total | Total | — | — | — | Total |
| M12 Relatórios | Total | Total | Caixa | — | — | Operacional |
| M13 Config | Total | Leitura | — | — | — | — |

**Nota**: "Total" = leitura + escrita + exclusão. "Parte" = acesso limitado a funcionalidades específicas.

---

## 5. Entidade: Usuário

### Dados Conceituais

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome completo do usuário |
| `login` | texto | Sim | Identificador único para acesso (UNIQUE) |
| `senha_hash` | texto | Sim | Hash bcrypt da senha (nunca armazenar texto puro) |
| `pin` | texto | Não | PIN de 4-6 dígitos para troca rápida (hash) |
| `perfil_id` | referência | Sim | Perfil de acesso atribuído |
| `funcionario_id` | referência | Não | Vínculo com cadastro de funcionário (M02) |
| `situacao` | enum | Sim | `ATIVO`, `BLOQUEADO`, `INATIVO` |
| `primeiro_acesso` | booleano | Sim | Se true, exige troca de senha |
| `tentativas_falhas` | inteiro | Sim | Contador de tentativas de login incorretas (default 0) |
| `ultimo_acesso` | timestamp | Não | Data/hora do último login bem-sucedido |
| `criado_em` | timestamp | Sim | Auditoria |
| `atualizado_em` | timestamp | Sim | Auditoria |

### Entidade: Perfil

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `nome` | texto | Sim | Nome do perfil (UNIQUE) |
| `descricao` | texto | Não | Descrição do perfil |
| `fixo` | booleano | Sim | Se true, não pode ser excluído (ex.: DONO) |
| `criado_em` | timestamp | Sim | Auditoria |

### Entidade: Permissão

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `perfil_id` | referência | Sim | Perfil associado |
| `modulo` | texto | Sim | Código do módulo (M00, M01, ...) |
| `funcionalidade` | texto | Sim | Funcionalidade específica (ex.: "pedido.criar", "caixa.fechar") |
| `leitura` | booleano | Sim | Pode visualizar |
| `escrita` | booleano | Sim | Pode criar/editar |
| `exclusao` | booleano | Sim | Pode excluir/cancelar |
| `aprovacao` | booleano | Sim | Pode aprovar/autorizar (ex.: sangria, cancelamento) |

### Entidade: Log de Auditoria

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `usuario_id` | referência | Sim | Quem executou a ação |
| `acao` | texto | Sim | Tipo de ação: `LOGIN`, `LOGOUT`, `CRIAR`, `EDITAR`, `EXCLUIR`, `CANCELAR`, `APROVAR` |
| `modulo` | texto | Sim | Módulo afetado |
| `entidade` | texto | Sim | Entidade afetada (ex.: "pedido", "produto") |
| `entidade_id` | texto | Não | ID da entidade afetada |
| `detalhe` | texto | Não | Descrição adicional ou diff resumido |
| `ip` | texto | Não | IP do dispositivo |
| `data_hora` | timestamp | Sim | Momento da ação |

---

## 6. Ciclo de Vida do Usuário

```
CRIADO (primeiro_acesso=true) → ATIVO → BLOQUEADO → ATIVO (desbloqueio)
                                   ↓
                                INATIVO (desligamento)
```

| Estado | Descrição | Quem altera |
|---|---|---|
| `ATIVO` | Usuário pode acessar o sistema normalmente | Dono (criação) |
| `BLOQUEADO` | Conta bloqueada por tentativas excessivas ou manualmente | Sistema (automático) ou Dono |
| `INATIVO` | Usuário desligado/desativado. Não pode logar. Dados mantidos para auditoria | Dono |

### Regras de Transição

- Conta é bloqueada automaticamente após N tentativas incorretas consecutivas (N configurável em M13, padrão: 5)
- Desbloqueio somente pelo Dono ou Gerente
- Inativação não exclui dados — mantém histórico para auditoria
- Usuário `INATIVO` não pode ser reativado (criar novo se necessário)

---

## 7. Fluxos Principais

### 7.1 Login Padrão

```
Usuário                          Sistema
   |                               |
   |-- Informa login + senha ----->|
   |                               |-- Valida credenciais
   |                               |-- Verifica situação (ATIVO?)
   |                               |-- Verifica tentativas_falhas < N?
   |                               |-- Se primeiro_acesso → redireciona para troca de senha
   |                               |-- Registra log LOGIN
   |                               |-- Cria sessão com timeout
   |<-- Redireciona para tela ------|
   |    inicial do perfil          |
```

### 7.2 Troca Rápida (PIN)

```
Usuário atual                    Sistema
   |                               |
   |-- Clica "Trocar Usuário" ---->|
   |                               |-- Exibe tela de PIN
Novo Usuário                       |
   |-- Digita PIN de 4-6 dígitos ->|
   |                               |-- Valida PIN
   |                               |-- Encerra sessão anterior (log LOGOUT)
   |                               |-- Inicia nova sessão (log LOGIN)
   |<-- Redireciona para tela ------|
   |    do novo perfil             |
```

### 7.3 Bloqueio Automático

```
Usuário                          Sistema
   |                               |
   |-- Informa login + senha ----->|
   |                               |-- Senha incorreta
   |                               |-- tentativas_falhas += 1
   |                               |-- Se tentativas_falhas >= N:
   |                               |     situacao = BLOQUEADO
   |                               |     Registra log BLOQUEIO
   |<-- "Conta bloqueada. ---------|
   |    Procure o responsável."    |
```

### 7.4 Gestão de Usuários (Dono)

```
Dono                             Sistema
   |                               |
   |-- Acessa Gestão de Usuários ->|
   |-- Cria novo usuário: -------->|
   |   nome, login, perfil,        |
   |   senha temporária            |
   |                               |-- Gera hash da senha
   |                               |-- primeiro_acesso = true
   |                               |-- situacao = ATIVO
   |                               |-- Registra log CRIAR
   |<-- Usuário criado ------------|
```

---

## 8. Regras de Negócio

| ID | Regra | Evidência |
|---|---|---|
| **RN-SEC-01** | Senhas armazenadas exclusivamente como hash bcrypt (custo ≥ 10). Nunca texto puro. | OWASP, Segurança |
| **RN-SEC-02** | Senha deve ter mínimo de 6 caracteres (parametrizável em M13). | Equilíbrio segurança × usabilidade para padaria |
| **RN-SEC-03** | PIN de troca rápida é exclusivo para dispositivos compartilhados e não substitui senha no login inicial do dia. | INF-02 |
| **RN-SEC-04** | O perfil DONO não pode ser excluído nem rebaixado. Sempre deve existir ao menos um usuário DONO. | Segurança |
| **RN-SEC-05** | Log de auditoria é somente inserção (append-only). Nenhum registro de auditoria pode ser editado ou excluído. | RNF04 |
| **RN-SEC-06** | Sessão expira após período de inatividade configurável (padrão: 30 min). Requer novo login. | Segurança |
| **RN-SEC-07** | Toda operação de escrita/exclusão deve verificar permissão do perfil do usuário logado antes de executar. | RNF03 |
| **RN-SEC-08** | Troca de senha no primeiro acesso é bloqueante: não permite acessar outras telas até concluir. | Segurança |
| **RN-SEC-09** | Reset de senha pelo Dono gera senha temporária e reativa `primeiro_acesso = true`. | PES-01 |
| **RN-SEC-10** | Tentativas de login com conta inexistente não revelam se o login existe ou não (mensagem genérica). | OWASP |

---

## 9. Requisitos de Interface

| Req | Descrição |
|---|---|
| UI-SEC-01 | Tela de login: apenas campos login + senha + botão "Entrar". Sem distrações. |
| UI-SEC-02 | Tela de troca rápida: teclado numérico grande para PIN (pensando em toque). |
| UI-SEC-03 | Menu lateral/superior mostra apenas os módulos que o perfil do usuário possui acesso. |
| UI-SEC-04 | Nome do usuário logado visível em todas as telas (canto superior). |
| UI-SEC-05 | Botão de logout sempre acessível. |
| UI-SEC-06 | Tela de gestão de usuários: lista com filtros por perfil e situação. |
| UI-SEC-07 | Indicador visual de sessões ativas (para dono): quem está logado, há quanto tempo, em qual dispositivo. |

---

## 10. Seed Data (Dados Iniciais)

O sistema deve ser entregue com os seguintes dados pré-cadastrados:

| Entidade | Dados iniciais |
|---|---|
| Perfis | DONO, GERENTE, CAIXA, ATENDENTE, CHAPEIRO, ADMINISTRATIVO (com permissões padrão da matriz §4.2) |
| Usuário | 1 usuário DONO com login `admin` e senha temporária (primeiro_acesso = true) |
| Permissões | Matriz completa conforme §4.2 |
