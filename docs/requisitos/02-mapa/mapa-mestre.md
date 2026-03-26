# Mapa-Mestre de Consolidação — Sistema Padaria

> **Status**: Fase 1 concluída  
> **Documentos analisados**:
> - `docs/padaria_historia_operabilidade.docx` → normalizado em `01-normalizados/padaria_historia_operabilidade.md`
> - `docs/padaria_objetivos_requisitos_iniciais.docx` → normalizado em `01-normalizados/padaria_objetivos_requisitos_iniciais.md`

---

## 1. Conflitos Identificados entre Documentos

### CONFLITO-001 — Stack Tecnológica — **RESOLVIDO**

| Aspecto | Documento de Requisitos (.docx) | Constitution do Projeto (.specify/memory) | **Decisão Final** |
|---|---|---|---|
| Frontend | React | ~~Angular~~ | **React** |
| Backend | Java | ~~.NET Core 3.1~~ | **Java Spring** |
| Banco | PostgreSQL | ~~Oracle / PL/SQL~~ | **PostgreSQL** |

- **Resolução**: Confirmado pelo responsável do projeto em 2026-03-26.
- **Stack definitiva**: React + Java Spring + PostgreSQL.
- `constitution.md` atualizado para refletir a decisão.
- ~~**Decisão Pendente**: `DP-STACK-001`~~ → **RESOLVIDA**.

---

### CONFLITO-002 — Escopo de Fiado: Qualquer Cliente vs. Apenas Cadastrados

| Documento | Posição |
|---|---|
| História (FIN-01) | Registro por cliente com nome, contato, data, itens/valor |
| Requisitos (RF12) | Cadastro de clientes que compram fiado |
| Requisitos (PL-06) | "O fiado continuará existindo para qualquer cliente ou apenas para clientes cadastrados?" |

- **Origem**: Pergunta PL-06 do doc de requisitos indica que essa definição está em aberto.
- **Severidade**: Média — Afeta modelagem de cliente/fiado.
- **Decisão Pendente**: `DP-FIADO-001` — Fiado será permitido para qualquer pessoa ou apenas para clientes previamente cadastrados?

---

## 2. Dúvidas em Aberto (Perguntas sem Resposta Definitiva)

> Extraídas da seção 8 do doc de requisitos. Nenhuma possui resposta confirmada pelo cliente.

| ID | Pergunta | Impacto se não resolvida |
|---|---|---|
| DUV-01 | Quantos computadores/dispositivos existirão na padaria? | Dimensionamento de infraestrutura e arquitetura de deploy |
| DUV-02 | Haverá apenas um ponto de atendimento ou mais? | Concorrência de caixa, modelagem de sessão |
| DUV-03 | Pedido no balcão, caixa, mesa ou múltiplos locais? | Fluxo de atendimento, telas necessárias |
| DUV-04 | Cozinha/chapa: monitor, impressora ou acompanhamento por voz? | Módulo de preparo, hardware necessário |
| DUV-05 | Comandas físicas, digitais ou ambas? | RF10, modelagem de comanda |
| DUV-06 | Fiado para qualquer cliente ou apenas cadastrados? | → DP-FIADO-001 |
| DUV-07 | Controlar receita por produto ou apenas vendas totais? | Granularidade dos relatórios, modelagem de venda |
| DUV-08 | Custo aproximado das vendas por iFood é necessário? | Módulo financeiro, integração iFood |
| DUV-09 | Quem registra limpeza, manutenção e troca de filtro? | Perfil de acesso, responsabilidades |
| DUV-10 | Funcionários aceitarão registrar ponto/vales em sistema? | Viabilidade de RF15, resistência organizacional |

---

## 3. Divergências Internas nos Documentos

### DIV-001 — Respostas Sugeridas vs. Decisões Confirmadas

As respostas da seção 3 do documento de operabilidade são **sugestões preliminares**, não decisões do cliente. Nenhuma foi confirmada.

- **Evidência**: "As respostas são sugestões preliminares, não decisões finais" (doc história, seção 3).
- **Impacto**: Nenhuma resposta sugerida pode ser tratada como requisito confirmado na SPEC.

### DIV-002 — Operação Offline

| Documento | Posição |
|---|---|
| História (INF-04) | "Caixa, pedidos internos e consulta básica devem funcionar offline" |
| Requisitos (ROP-08) | "Uso predominantemente via navegador, em rede local ou internet" |
| Requisitos (RNF) | Não há RNF explícito sobre funcionamento offline |

- **Impacto**: Se offline for obrigatório, afeta significativamente a arquitetura (PWA, service workers, sincronização).
- **Decisão Pendente**: `DP-OFFLINE-001` — O sistema precisa funcionar offline? Em que nível?

---

## 4. Sobreposições e Alinhamentos Confirmados

Os documentos **concordam** nos seguintes pontos (sem conflito):

| Tema | Alinhamento |
|---|---|
| Interface simples | Ambos enfatizam: poucos cliques, botões claros, acessível a usuários com pouca experiência. |
| Perfis de acesso | Atendente, caixa, dono/gerente, cozinha/produção. |
| Fiado formalizado | Substituir caderninho por registro digital com histórico. |
| Controle de estoque duplo | Ingredientes/insumos + produtos prontos. |
| Vales com rastreio | Registrar retirada no ato: valor, funcionário, data, motivo. |
| Manutenção/higiene | Checklists e registros simples de equipamentos e limpeza. |
| Módulos 1–6 | Estrutura modular coerente com os RFs identificados. |
| Relatórios gerenciais | Visão de vendas, estoque, fiado, desperdício, vales, custos. |

---

## 5. Registro de Decisões Pendentes (Consolidado)

| ID | Descrição | Origem | Severidade | Bloqueia |
|---|---|---|---|---|
| DP-STACK-001 | Stack: React + Java Spring + PostgreSQL | constitution.md vs docx | **ALTA** | ~~Toda a implementação~~ **RESOLVIDA** |
| DP-FIADO-001 | Fiado para qualquer pessoa ou apenas clientes cadastrados? | PL-06 | Média | Modelagem cliente/fiado |
| DP-OFFLINE-001 | Sistema precisa funcionar offline? Em que nível? | INF-04, ROP-08 | Média-Alta | Arquitetura frontend |
| DP-RECEITA-001 | Controlar receita/custo por produto ou apenas vendas totais? | PL-07 | Média | Modelagem de venda e relatórios |
| DP-COZINHA-001 | Cozinha terá monitor, impressora ou voz? | PL-04 | Média | Módulo preparo, hardware |
| DP-COMANDAS-001 | Comandas físicas, digitais ou ambas? | PL-05 | Média | RF10, UX do atendimento |
| DP-IFOOD-001 | É necessário calcular custo/lucro das vendas iFood? | PL-08 | Média | Módulo financeiro |

---

## 6. Próximo Passo

Conforme o fluxo obrigatório:
- **As decisões pendentes marcadas como ALTA (DP-STACK-001) devem ser resolvidas antes de gerar qualquer SPEC.**
- As demais decisões podem ser resolvidas durante a elaboração da SPEC, desde que registradas.
- Nenhuma sugestão preliminar pode ser tratada como requisito confirmado sem validação do cliente.

> **AÇÃO NECESSÁRIA**: O responsável pelo projeto deve confirmar:
> 1. Stack tecnológica definitiva
> 2. Respostas às perguntas PL-01 a PL-10
