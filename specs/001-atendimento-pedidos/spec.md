# SPEC-001 — Atendimento e Pedidos

> **Módulo**: 3 – Atendimento e Pedidos  
> **Status**: Rascunho (Fase 2)  
> **Data**: 2026-03-26  
> **Fluxo crítico**: Registro de pedidos (balcão, mesa, viagem, delivery) + fila de preparo + comandas  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/02-mapa/mapa-mestre.md`

---

## 1. Objetivo do Fluxo

Permitir que a padaria registre, acompanhe e finalize pedidos de todas as modalidades de atendimento (balcão, mesa, viagem e delivery), com visibilidade para preparo na cozinha/chapa e controle por meio de comandas, de forma simples e acessível a usuários com pouca experiência digital.

### Evidências

- **História §1.4**: "pedidos podem ser improvisados e a rotina fica desorganizada nos picos"
- **História §1.5**: "Padaria possui mesas para consumo no local [...] mais pedidos, mais louça ou lixo"
- **História §1.8**: "padaria passou a entregar pelo iFood [...] custo alto"
- **OE-02**: "Registrar vendas de balcão, mesas, entrega própria e plataformas de delivery"
- **Quadro de Problemas (Atendimento)**: "Experiência ruim do cliente, retrabalho da equipe"

---

## 2. Requisitos Funcionais Cobertos

| RF | Descrição | Origem |
|---|---|---|
| **RF07** | Registrar vendas de balcão e consolidar itens vendidos em cada atendimento. | Requisitos §5 |
| **RF08** | Registrar pedidos para consumo em mesas: mesa, itens, situação do pedido. | Requisitos §5 |
| **RF09** | Registrar pedidos para viagem e entrega, separando presenciais de plataformas. | Requisitos §5 |
| **RF10** | Controlar comandas ou pedidos abertos até sua finalização. | Requisitos §5 |
| **RF18** | Identificar pedidos pendentes de preparo para cozinha/chapa: aguardando, em preparo, concluído. | Requisitos §5 |

### Requisitos Não Funcionais Aplicáveis

| RNF | Relevância para este fluxo |
|---|---|
| **RNF01** | Interface simples, poucos passos — atendente opera em ritmo de balcão. |
| **RNF02** | Utilizável por pessoas com pouca familiaridade digital. |
| **RNF03** | Perfis de acesso: atendente, caixa, cozinha — cada um vê apenas o que precisa. |
| **RNF04** | Auditoria: data, hora, usuário em cada pedido. |
| **RNF07** | Tempo de resposta compatível com atendimento em balcão. |
| **RNF11** | Linguagem cotidiana da padaria nos rótulos e telas. |

### Requisitos Operacionais Aplicáveis

| ROP | Descrição |
|---|---|
| **ROP-01** | Pelo menos um ponto de atendimento para lançamento de pedidos. |
| **ROP-02** | Menor número possível de cliques para abrir, editar e finalizar pedido. |
| **ROP-03** | Pedidos para cozinha/chapa visíveis com prioridade e situação. |
| **ROP-04** | Comandas com abertura e fechamento simples. |
| **ROP-07** | Operações críticas possíveis por usuários de pouca alfabetização digital. |

---

## 3. Atores

| Ator | Papel neste fluxo |
|---|---|
| **Atendente** | Registra pedidos (balcão, mesa, viagem, delivery). Adiciona/remove itens. |
| **Caixa** | Finaliza/fecha comandas. Registra forma de pagamento (interface com Módulo 4). |
| **Chapeiro / Cozinha** | Visualiza fila de preparo. Atualiza status do pedido (aguardando → em preparo → concluído). |
| **Dono / Gerente** | Consulta pedidos, cancela pedidos (se necessário), supervisiona. |

**Evidência**: INF-06 e RNF03 — "Perfis de uso diferentes: atendente, caixa, dono/gerente, cozinha/produção."

---

## 4. Modalidades de Pedido

| Modalidade | Código | Descrição | Evidência |
|---|---|---|---|
| Balcão | `BALCAO` | Cliente compra e leva na hora. Não vincula a mesa. | RF07, ATD-02 |
| Mesa | `MESA` | Vinculado a uma mesa (comanda). Pode ter múltiplos itens ao longo do tempo. | RF08, ATD-05 |
| Viagem | `VIAGEM` | Cliente leva para casa. Não vincula a mesa nem a plataforma. | RF09 |
| Delivery | `DELIVERY` | Pedido de plataforma (ex.: iFood) ou entrega própria. Classificação separada para análise de custo. | RF09, ATD-07 |

**Regra**: Todos usam a mesma base de registro, diferenciados por `modalidade`.

**Evidência**: ATD-02 — "Mesma base de registro, com identificação clara do tipo de atendimento."

---

## 5. Estados do Pedido (Ciclo de Vida)

```
ABERTO → EM_PREPARO → PRONTO → ENTREGUE → FINALIZADO
                                              ↓
                                          CANCELADO
```

| Estado | Descrição | Quem altera |
|---|---|---|
| `ABERTO` | Pedido criado, itens registrados. Aparece na fila de preparo. | Atendente |
| `EM_PREPARO` | Cozinha/chapa acusou recebimento e iniciou preparo. | Chapeiro/Cozinha |
| `PRONTO` | Preparo concluído. Aguardando entrega ao cliente ou retirada. | Chapeiro/Cozinha |
| `ENTREGUE` | Itens entregues ao cliente (balcão, mesa ou saiu para entrega). | Atendente |
| `FINALIZADO` | Pagamento registrado e pedido encerrado. | Caixa |
| `CANCELADO` | Pedido cancelado antes da finalização. Requer motivo e registro. | Dono/Gerente ou Caixa |

**Evidência**: RF18 — "aguardando, em preparo e concluído" + extensão lógica para fluxo completo.

### Regras de Transição

- `ABERTO` → `EM_PREPARO`: somente quando cozinha/chapa acusar.
- `EM_PREPARO` → `PRONTO`: somente quando preparo concluído.
- `PRONTO` → `ENTREGUE`: somente quando cliente recebeu.
- `ENTREGUE` → `FINALIZADO`: somente após registro de pagamento (interface com Módulo 4).
- Qualquer estado anterior a `FINALIZADO` → `CANCELADO`: com motivo obrigatório e registro de quem cancelou.
- `FINALIZADO` e `CANCELADO` são estados terminais (imutáveis).

---

## 6. Comanda (RF10)

### Conceito

A comanda é o agrupador de pedidos para atendimento em **mesa**. Uma comanda pode acumular múltiplos itens ao longo do tempo (cliente pede mais depois).

### Ciclo de Vida da Comanda

```
ABERTA → FECHADA → PAGA
```

| Estado | Descrição |
|---|---|
| `ABERTA` | Comanda ativa, aceitando adição de itens. Vinculada a uma mesa. |
| `FECHADA` | Atendimento encerrado. Não aceita novos itens. Aguarda pagamento. |
| `PAGA` | Pagamento registrado (interface com Módulo 4). Estado terminal. |

### Regras

- Uma mesa só pode ter **uma comanda aberta** por vez.
- Comanda vincula: mesa, itens (com horário de adição), atendente responsável.
- Fechamento da comanda consolida todos os itens para pagamento.
- Após `PAGA`, a mesa fica disponível para nova comanda.

**Evidência**: ATD-05 — "Numeradas e vinculadas à mesa ou cliente" | ATD-06 — "Comanda concentra tudo consumido; encerrada no caixa ao final."

### Decisão Pendente

> **DP-COMANDAS-001**: Comandas serão físicas, digitais ou ambas? (DUV-05 no mapa-mestre)
> 
> **Premissa adotada para esta SPEC**: Comandas digitais no sistema. Se houver necessidade de comanda física complementar, será tratada como extensão futura. Esta premissa **não é confirmada** — pode mudar.

---

## 7. Fila de Preparo / Visão Cozinha (RF18)

### Conceito

Tela específica para cozinha/chapa que mostra os pedidos que precisam ser preparados, em ordem de chegada, com prioridade visual.

### O que a tela deve mostrar

| Informação | Origem |
|---|---|
| Número do pedido | Gerado pelo sistema |
| Modalidade | `BALCAO`, `MESA` (+ nº mesa), `VIAGEM`, `DELIVERY` |
| Itens do pedido | Lista de produtos com quantidade |
| Horário de abertura | Timestamp do pedido |
| Status atual | `ABERTO` (aguardando) ou `EM_PREPARO` |
| Tempo decorrido | Calculado a partir do horário de abertura |

### Ações disponíveis na tela de cozinha

| Ação | Efeito |
|---|---|
| **Iniciar preparo** | Status → `EM_PREPARO` |
| **Concluir preparo** | Status → `PRONTO` |

### Regras

- Pedidos `ABERTO` aparecem agrupados como "Aguardando".
- Pedidos `EM_PREPARO` aparecem agrupados como "Em preparo".
- Pedidos `PRONTO` desaparecem da fila após confirmação (ou ficam visíveis por tempo parametrizável).
- Ordenação padrão: mais antigo primeiro (FIFO). 
- Identificação visual clara de modalidade (ícone ou cor por tipo).

**Evidência**: ROP-03 — "Pedidos para cozinha/chapa visíveis com identificação de prioridade e situação atual."

### Decisão Pendente

> **DP-COZINHA-001**: Cozinha terá monitor, impressora ou voz? (DUV-04 no mapa-mestre)
> 
> **Premissa adotada para esta SPEC**: Tela em monitor (navegador web). Impressão como extensão futura. Premissa **não confirmada**.

---

## 8. Dados do Pedido (Estrutura Conceitual)

> **NOTA**: Isto NÃO é modelagem de banco — é estrutura conceitual para contratos.

### Pedido

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `numero` | sequencial diário | Sim | Número do pedido visível (ex.: #001, #002) |
| `modalidade` | enum | Sim | `BALCAO`, `MESA`, `VIAGEM`, `DELIVERY` |
| `status` | enum | Sim | `ABERTO`, `EM_PREPARO`, `PRONTO`, `ENTREGUE`, `FINALIZADO`, `CANCELADO` |
| `mesa_id` | referência | Condicional | Obrigatório se modalidade = `MESA` |
| `comanda_id` | referência | Condicional | Obrigatório se modalidade = `MESA` |
| `plataforma` | texto | Condicional | Nome da plataforma se modalidade = `DELIVERY` (ex.: "iFood", "Entrega própria") |
| `data_abertura` | timestamp | Sim | Quando o pedido foi criado |
| `data_finalizacao` | timestamp | Não | Quando o pedido foi finalizado ou cancelado |
| `atendente_id` | referência | Sim | Quem registrou o pedido |
| `observacao` | texto | Não | Observações gerais do pedido |
| `motivo_cancelamento` | texto | Condicional | Obrigatório se status = `CANCELADO` |

### Item do Pedido

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `pedido_id` | referência | Sim | Pedido ao qual pertence |
| `produto_id` | referência | Sim | Produto do catálogo (RF01) |
| `quantidade` | numérico | Sim | Quantidade solicitada |
| `preco_unitario` | monetário | Sim | Preço no momento da venda (snapshot) |
| `observacao_item` | texto | Não | Ex.: "sem cebola", "bem passado" |
| `data_adicao` | timestamp | Sim | Quando o item foi adicionado |

### Comanda

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `id` | identificador | Sim | Gerado pelo sistema |
| `numero` | sequencial | Sim | Número visível da comanda |
| `mesa_id` | referência | Sim | Mesa vinculada |
| `status` | enum | Sim | `ABERTA`, `FECHADA`, `PAGA` |
| `data_abertura` | timestamp | Sim | Quando foi aberta |
| `data_fechamento` | timestamp | Não | Quando foi fechada |
| `atendente_id` | referência | Sim | Quem abriu a comanda |

---

## 9. Regras de Negócio

| ID | Regra | Evidência |
|---|---|---|
| **RN-ATD-01** | Todo pedido deve ter pelo menos um item para sair do estado `ABERTO`. | Lógica de integridade |
| **RN-ATD-02** | O preço do item no pedido é o preço do produto **no momento da venda** (snapshot), não referência dinâmica. | Prática de integridade financeira |
| **RN-ATD-03** | Cancelamento de pedido exige motivo e registro de quem cancelou (auditoria RNF04). | RNF04 |
| **RN-ATD-04** | Uma mesa só pode ter uma comanda `ABERTA` por vez. | ATD-05, ATD-06 — evitar confusão de controle |
| **RN-ATD-05** | Pedidos `DELIVERY` devem registrar a plataforma de origem para análise separada de custos. | ATD-07, ATD-08 |
| **RN-ATD-06** | Itens podem ser adicionados a um pedido/comanda enquanto o status for `ABERTO` ou `EM_PREPARO`. | Realidade da padaria — cliente pede mais |
| **RN-ATD-07** | Remoção de item de pedido `EM_PREPARO` ou posterior requer justificativa (auditoria). | RNF04, práctica operacional |
| **RN-ATD-08** | O número do pedido reinicia diariamente (sequencial do dia). | Simplicidade operacional — padaria pequena |
| **RN-ATD-09** | Valores monetários parametrizáveis (regra constitution: "todo valor que definir fluxo deve ser parametrizado"). | Constitution / activeContext |
| **RN-ATD-10** | O tempo de exibição de pedidos `PRONTO` na fila de cozinha deve ser parametrizável. | Flexibilidade operacional |

---

## 10. Fluxos Principais

### 10.1 Pedido de Balcão

```
Atendente                              Sistema                      Cozinha
    |                                    |                             |
    |-- Abre novo pedido (BALCAO) ------>|                             |
    |-- Seleciona itens (produto+qtd) -->|                             |
    |-- [Observação opcional] ---------->|                             |
    |-- Confirma pedido ---------------->|-- Cria pedido ABERTO ------>|
    |                                    |-- Envia para fila --------->|
    |                                    |                             |-- Inicia preparo
    |                                    |                             |-- Conclui preparo
    |                                    |<-- Status = PRONTO ---------|
    |<-- Notifica PRONTO ----------------|                             |
    |-- Entrega ao cliente ------------->|-- Status = ENTREGUE         |
    |-- [Encaminha para caixa] --------->|-- (Módulo 4: pagamento)     |
    |                                    |-- Status = FINALIZADO       |
```

### 10.2 Pedido de Mesa (com Comanda)

```
Atendente                              Sistema                      Cozinha
    |                                    |                             |
    |-- Abre comanda (mesa X) --------->|-- Cria comanda ABERTA       |
    |-- Registra pedido (MESA) -------->|-- Vincula à comanda         |
    |-- Seleciona itens --------------->|                             |
    |-- Confirma ---------------------->|-- Pedido ABERTO ----------->|
    |                                    |                             |-- Prepara
    |                                    |                             |-- PRONTO
    |                                    |                             |
    |   (cliente pede mais)              |                             |
    |-- Adiciona itens à comanda ------>|-- Novo pedido/itens ------->|
    |                                    |                             |-- Prepara
    |                                    |                             |
    |   (cliente pede a conta)           |                             |
    |-- Fecha comanda ----------------->|-- Comanda FECHADA           |
    |-- [Encaminha para caixa] -------->|-- (Módulo 4: pagamento)     |
    |                                    |-- Comanda PAGA             |
    |                                    |-- Mesa liberada             |
```

### 10.3 Pedido de Viagem

Igual ao fluxo de balcão, com `modalidade = VIAGEM`. Sem vinculação a mesa.

### 10.4 Pedido de Delivery

```
Atendente                              Sistema
    |                                    |
    |-- Abre pedido (DELIVERY) -------->|
    |-- Informa plataforma (iFood etc)->|
    |-- Seleciona itens --------------->|
    |-- Confirma ---------------------->|-- Pedido ABERTO → fila cozinha
    |                                    |-- Classificação DELIVERY separada
    |   (preparo e finalização seguem fluxo padrão)
```

**Diferencial**: Pedidos `DELIVERY` são classificados separadamente para que relatórios possam cruzar faturamento × custos da plataforma (RF19/ATD-08).

---

## 11. Interfaces com Outros Módulos

| Módulo | Interface | Direção |
|---|---|---|
| **Módulo 1 – Cadastros** | Consulta de produtos (nome, preço, categoria, disponibilidade). Consulta de mesas. | Atendimento **consome** cadastros |
| **Módulo 4 – Caixa/Financeiro** | Ao finalizar pedido ou fechar comanda → registro de pagamento, forma, fiado. | Atendimento **envia** para caixa |
| **Módulo 2 – Estoque** | Ao finalizar pedido → baixa de estoque dos produtos vendidos (futuro). | Atendimento **notifica** estoque |
| **Módulo 6 – Relatórios** | Dados de pedidos alimentam relatórios de vendas, volume, delivery. | Atendimento **fornece** dados |

> **NOTA**: Esta SPEC não define contratos de API, modelagem de banco nem implementação dos módulos adjacentes. Apenas registra os pontos de interface.

---

## 12. Decisões Pendentes que Afetam este Fluxo

| ID | Decisão | Impacto nesta SPEC | Status |
|---|---|---|---|
| **DP-COMANDAS-001** | Comandas físicas, digitais ou ambas? | Seção 6 — premissa: digital. | Pendente |
| **DP-COZINHA-001** | Monitor, impressora ou voz na cozinha? | Seção 7 — premissa: monitor web. | Pendente |
| **DP-OFFLINE-001** | Funcionamento offline? | Se sim, afeta toda a arquitetura deste fluxo. | Pendente |
| **DP-IFOOD-001** | Calcular custo/lucro iFood? | Seção 10.4 — classificação separada é pré-requisito, mas detalhamento é do Módulo 4/6. | Pendente |
| **DUV-02** | Mais de um ponto de atendimento? | Concorrência de pedidos simultâneos. | Pendente |
| **DUV-03** | Pedido no balcão, caixa, mesa ou múltiplos? | Telas necessárias, perfis de acesso. | Pendente |

---

## 13. Premissas Adotadas (não confirmadas)

> Conforme regra do projeto: premissas não confirmadas **não são decisões**. Podem mudar.

| # | Premissa | Seção |
|---|---|---|
| P-01 | Comandas digitais no sistema (sem comanda física por enquanto). | §6 |
| P-02 | Tela de cozinha em monitor via navegador (sem impressão por enquanto). | §7 |
| P-03 | Número do pedido reinicia diariamente. | §9, RN-ATD-08 |
| P-04 | Um único ponto de atendimento para a primeira versão. | ROP-01 |
| P-05 | Não há integração direta com API do iFood — registro manual de pedidos delivery. | Escopo simplificado §4 dos requisitos |

---

## 14. Critérios de Aceite (alto nível)

| # | Critério |
|---|---|
| CA-01 | Atendente consegue abrir e registrar um pedido de balcão com 3 itens em ≤ 5 cliques. |
| CA-02 | Chapeiro visualiza pedido na fila de preparo imediatamente após confirmação. |
| CA-03 | Transição de status funciona: ABERTO → EM_PREPARO → PRONTO → ENTREGUE → FINALIZADO. |
| CA-04 | Comanda de mesa acumula itens de múltiplos pedidos e consolida no fechamento. |
| CA-05 | Pedido cancelado exige motivo e gera registro de auditoria. |
| CA-06 | Pedido DELIVERY é classificado com plataforma e aparece separado nos dados. |
| CA-07 | Uma mesa com comanda ABERTA não permite abrir segunda comanda. |
| CA-08 | Interface legível, botões claros, linguagem cotidiana — validável com usuário leigo. |

---

## 15. Fora do Escopo desta SPEC

- Modelagem de banco de dados (será na PLAN/TASKS).
- Contratos de API / DTOs (será na PLAN).
- Implementação de código.
- Módulo de pagamento (Módulo 4 — SPEC própria).
- Integração real com API iFood.
- Relatórios detalhados (Módulo 6 — SPEC própria).
- Baixa automática de estoque (Módulo 2 — interface apenas registrada).
