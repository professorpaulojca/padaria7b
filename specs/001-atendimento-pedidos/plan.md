# PLAN-001 — Atendimento e Pedidos

> **SPEC de referência**: SPEC-001  
> **Status**: Rascunho (Fase 3)  
> **Data**: 2026-03-26  
> **Stack**: React + Java Spring Boot + PostgreSQL  
> **Migração**: Flyway

---

## 1. Objetivo deste Documento

Registrar **decisões técnicas** para implementar a SPEC-001 (Atendimento e Pedidos). Este documento NÃO contém tarefas nem código — apenas decisões de arquitetura, modelagem, contratos e componentes.

---

## 2. Modelagem de Banco de Dados (PostgreSQL)

### 2.1 Tabelas

Todas as tabelas usam **lowercase + underscore**, conforme convention do projeto.

#### `pedido`

| Coluna | Tipo | Constraint | Descrição |
|---|---|---|---|
| `id` | `BIGSERIAL` | PK | Identificador único |
| `numero` | `INTEGER` | NOT NULL | Número sequencial diário (RN-ATD-08) |
| `modalidade` | `VARCHAR(20)` | NOT NULL, CHECK | `BALCAO`, `MESA`, `VIAGEM`, `DELIVERY` |
| `status` | `VARCHAR(20)` | NOT NULL, CHECK | `ABERTO`, `EM_PREPARO`, `PRONTO`, `ENTREGUE`, `FINALIZADO`, `CANCELADO` |
| `mesa_id` | `BIGINT` | FK → mesa(id), NULLABLE | Obrigatório se modalidade = MESA |
| `comanda_id` | `BIGINT` | FK → comanda(id), NULLABLE | Obrigatório se modalidade = MESA |
| `plataforma` | `VARCHAR(100)` | NULLABLE | Nome plataforma se DELIVERY (ex.: "iFood") |
| `atendente_id` | `BIGINT` | FK → usuario(id), NOT NULL | Quem registrou |
| `observacao` | `TEXT` | NULLABLE | Observações gerais |
| `motivo_cancelamento` | `TEXT` | NULLABLE | Obrigatório se CANCELADO |
| `data_abertura` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Criação |
| `data_finalizacao` | `TIMESTAMPTZ` | NULLABLE | Finalização ou cancelamento |
| `criado_em` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Auditoria |
| `atualizado_em` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Auditoria |

**CHECK constraints**:
```sql
CHECK (modalidade IN ('BALCAO','MESA','VIAGEM','DELIVERY'))
CHECK (status IN ('ABERTO','EM_PREPARO','PRONTO','ENTREGUE','FINALIZADO','CANCELADO'))
CHECK (modalidade <> 'MESA' OR (mesa_id IS NOT NULL AND comanda_id IS NOT NULL))
CHECK (modalidade <> 'DELIVERY' OR plataforma IS NOT NULL)
CHECK (status <> 'CANCELADO' OR motivo_cancelamento IS NOT NULL)
```

#### `item_pedido`

| Coluna | Tipo | Constraint | Descrição |
|---|---|---|---|
| `id` | `BIGSERIAL` | PK | Identificador único |
| `pedido_id` | `BIGINT` | FK → pedido(id), NOT NULL | Pedido pai |
| `produto_id` | `BIGINT` | FK → produto(id), NOT NULL | Produto do catálogo (Módulo 1) |
| `quantidade` | `INTEGER` | NOT NULL, CHECK > 0 | Quantidade solicitada |
| `preco_unitario` | `NUMERIC(10,2)` | NOT NULL | Snapshot do preço no momento da venda (RN-ATD-02) |
| `observacao_item` | `TEXT` | NULLABLE | Ex.: "sem cebola" |
| `data_adicao` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Quando adicionado |

#### `comanda`

| Coluna | Tipo | Constraint | Descrição |
|---|---|---|---|
| `id` | `BIGSERIAL` | PK | Identificador único |
| `numero` | `INTEGER` | NOT NULL | Número visível sequencial |
| `mesa_id` | `BIGINT` | FK → mesa(id), NOT NULL | Mesa vinculada |
| `status` | `VARCHAR(20)` | NOT NULL, CHECK | `ABERTA`, `FECHADA`, `PAGA` |
| `atendente_id` | `BIGINT` | FK → usuario(id), NOT NULL | Quem abriu |
| `data_abertura` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Abertura |
| `data_fechamento` | `TIMESTAMPTZ` | NULLABLE | Fechamento |
| `criado_em` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Auditoria |
| `atualizado_em` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | Auditoria |

**CHECK**: `CHECK (status IN ('ABERTA','FECHADA','PAGA'))`

**UNIQUE parcial** (RN-ATD-04): Uma mesa só pode ter uma comanda ABERTA:
```sql
CREATE UNIQUE INDEX uq_comanda_mesa_aberta 
  ON comanda(mesa_id) 
  WHERE status = 'ABERTA';
```

### 2.2 Índices

```sql
CREATE INDEX idx_pedido_status ON pedido(status);
CREATE INDEX idx_pedido_data_abertura ON pedido(data_abertura);
CREATE INDEX idx_pedido_modalidade ON pedido(modalidade);
CREATE INDEX idx_pedido_comanda_id ON pedido(comanda_id);
CREATE INDEX idx_item_pedido_pedido_id ON item_pedido(pedido_id);
CREATE INDEX idx_comanda_mesa_id ON comanda(mesa_id);
CREATE INDEX idx_comanda_status ON comanda(status);
```

### 2.3 Sequência Diária do Pedido (RN-ATD-08)

Decisão: **Calcular `numero` via query** no momento da criação:

```sql
SELECT COALESCE(MAX(numero), 0) + 1 
FROM pedido 
WHERE data_abertura::DATE = CURRENT_DATE;
```

Executado dentro de transação com lock para evitar duplicatas. Alternativa futura: tabela `sequencia_diaria` com `FOR UPDATE`.

### 2.4 Flyway — Convenção de Migrations

- Diretório: `src/main/resources/db/migration/`
- Nomenclatura: `V{NNN}__{descricao}.sql` (ex.: `V001__criar_tabela_pedido.sql`)
- Cada migration atômica — uma tabela ou alteração por arquivo.

---

## 3. API REST (Java Spring Boot)

### 3.1 Convenções

- Base path: `/api/v1`
- JSON camelCase nos DTOs (ex.: `dataAbertura`, `mesaId`)
- Banco lowercase underscore (mapeado via JPA `@Column(name="...")`)
- Respostas de erro padronizadas: `{ "error": "...", "code": "...", "details": [...] }`
- Timestamps em ISO 8601 com timezone (ex.: `2026-03-26T14:30:00-03:00`)

### 3.2 Endpoints — Pedido

| Método | Rota | Descrição | Request Body | Response |
|---|---|---|---|---|
| `POST` | `/api/v1/pedidos` | Criar pedido | `CriarPedidoRequest` | `PedidoResponse` (201) |
| `GET` | `/api/v1/pedidos` | Listar pedidos (filtros) | Query params | `Page<PedidoResumoResponse>` |
| `GET` | `/api/v1/pedidos/{id}` | Detalhe do pedido | — | `PedidoResponse` |
| `PATCH` | `/api/v1/pedidos/{id}/status` | Transição de status | `AlterarStatusRequest` | `PedidoResponse` |
| `POST` | `/api/v1/pedidos/{id}/itens` | Adicionar item ao pedido | `AdicionarItemRequest` | `ItemPedidoResponse` (201) |
| `DELETE` | `/api/v1/pedidos/{id}/itens/{itemId}` | Remover item (com justificativa) | `RemoverItemRequest` | 204 |
| `POST` | `/api/v1/pedidos/{id}/cancelar` | Cancelar pedido | `CancelarPedidoRequest` | `PedidoResponse` |

### 3.3 Endpoints — Comanda

| Método | Rota | Descrição | Request Body | Response |
|---|---|---|---|---|
| `POST` | `/api/v1/comandas` | Abrir comanda para mesa | `AbrirComandaRequest` | `ComandaResponse` (201) |
| `GET` | `/api/v1/comandas/{id}` | Detalhe da comanda com todos os pedidos/itens | — | `ComandaDetalhadaResponse` |
| `PATCH` | `/api/v1/comandas/{id}/fechar` | Fechar comanda | — | `ComandaResponse` |
| `GET` | `/api/v1/mesas/{mesaId}/comanda-ativa` | Buscar comanda ativa da mesa | — | `ComandaResponse` ou 404 |

### 3.4 Endpoints — Fila de Preparo (Cozinha)

| Método | Rota | Descrição | Response |
|---|---|---|---|
| `GET` | `/api/v1/cozinha/fila` | Pedidos ABERTO + EM_PREPARO ordenados FIFO | `List<PedidoCozinhaResponse>` |

> A transição de status da cozinha usa `PATCH /api/v1/pedidos/{id}/status` com `novoStatus = EM_PREPARO` ou `PRONTO`.

### 3.5 DTOs Principais

#### `CriarPedidoRequest`
```json
{
  "modalidade": "BALCAO",
  "mesaId": null,
  "plataforma": null,
  "observacao": null,
  "itens": [
    { "produtoId": 1, "quantidade": 2, "observacaoItem": "sem cebola" }
  ]
}
```

#### `PedidoResponse`
```json
{
  "id": 1,
  "numero": 15,
  "modalidade": "BALCAO",
  "status": "ABERTO",
  "mesaId": null,
  "comandaId": null,
  "plataforma": null,
  "observacao": null,
  "dataAbertura": "2026-03-26T14:30:00-03:00",
  "dataFinalizacao": null,
  "atendente": { "id": 1, "nome": "Maria" },
  "itens": [
    {
      "id": 1,
      "produtoId": 1,
      "produtoNome": "Pão Francês",
      "quantidade": 2,
      "precoUnitario": 0.75,
      "observacaoItem": "sem cebola",
      "dataAdicao": "2026-03-26T14:30:00-03:00"
    }
  ],
  "totalEstimado": 1.50
}
```

#### `AlterarStatusRequest`
```json
{
  "novoStatus": "EM_PREPARO"
}
```

#### `CancelarPedidoRequest`
```json
{
  "motivoCancelamento": "Cliente desistiu"
}
```

#### `PedidoCozinhaResponse`
```json
{
  "id": 1,
  "numero": 15,
  "modalidade": "MESA",
  "mesaNumero": 3,
  "status": "ABERTO",
  "dataAbertura": "2026-03-26T14:30:00-03:00",
  "tempoDecorridoMinutos": 8,
  "itens": [
    { "produtoNome": "X-Salada", "quantidade": 1, "observacaoItem": null }
  ]
}
```

### 3.6 Validações no Backend (Spring Validation)

| Regra | Implementação |
|---|---|
| RN-ATD-01 (ao menos 1 item) | `@Size(min=1)` em `CriarPedidoRequest.itens` |
| RN-ATD-02 (snapshot preço) | Service busca preço atual do produto e grava no `item_pedido.preco_unitario` |
| RN-ATD-03 (cancelamento + motivo) | Validação no `CancelarPedidoRequest`: `@NotBlank motivoCancelamento` |
| RN-ATD-04 (uma comanda por mesa) | Unique index + verificação no service antes de INSERT |
| RN-ATD-05 (plataforma DELIVERY) | CHECK no banco + validação condicional no DTO |
| RN-ATD-06 (adicionar itens em ABERTO/EM_PREPARO) | Validação no `AdicionarItemService` |
| RN-ATD-07 (remoção com justificativa se EM_PREPARO+) | Validação condicional + coluna `justificativa_remocao` em log |
| Transições de status válidas | State machine via `StatusTransicaoValidator` |

### 3.7 Arquitetura de Camadas (Spring Boot)

```
controller/
  PedidoController.java
  ComandaController.java
  CozinhaController.java

dto/
  request/
    CriarPedidoRequest.java
    AlterarStatusRequest.java
    CancelarPedidoRequest.java
    AdicionarItemRequest.java
    RemoverItemRequest.java
    AbrirComandaRequest.java
  response/
    PedidoResponse.java
    PedidoResumoResponse.java
    PedidoCozinhaResponse.java
    ItemPedidoResponse.java
    ComandaResponse.java
    ComandaDetalhadaResponse.java

entity/
  Pedido.java
  ItemPedido.java
  Comanda.java

repository/
  PedidoRepository.java
  ItemPedidoRepository.java
  ComandaRepository.java

service/
  PedidoService.java
  ComandaService.java
  CozinhaService.java

validation/
  StatusTransicaoValidator.java
```

### 3.8 Transições de Status — State Machine

Implementação via mapa de transições válidas no `StatusTransicaoValidator`:

```
ABERTO       → [EM_PREPARO, CANCELADO]
EM_PREPARO   → [PRONTO, CANCELADO]
PRONTO       → [ENTREGUE, CANCELADO]
ENTREGUE     → [FINALIZADO, CANCELADO]
FINALIZADO   → []  (terminal)
CANCELADO    → []  (terminal)
```

Validação antes de qualquer `PATCH /status`. Transição inválida retorna `400 Bad Request`.

---

## 4. Frontend React — Arquitetura de Componentes

### 4.1 Tecnologias Escolhidas

| Tecnologia | Justificativa |
|---|---|
| **React 18+** | Stack confirmada |
| **React Router v6** | Navegação SPA |
| **Axios** | HTTP client |
| **Zustand** | State management leve (padaria não precisa de Redux) |
| **CSS Modules** ou **Tailwind CSS** | Estilização (decisão pode ser refinada no protótipo) |
| **React Icons** | Ícones (ROP-02: muitos cliques em ícones, poucos campos) |
| **React Toastify** | Notificações |

### 4.2 Estrutura de Pastas

```
src/
  pages/
    atendimento/
      NovosPedidos.jsx          — Tela principal de criação de pedido
      SelecionarProdutos.jsx    — Grid de produtos com ícones/cards
      DetalhePedido.jsx          — Visualização de pedido individual
    cozinha/
      FilaPreparo.jsx            — Painel da cozinha (fila de pedidos)
    mesas/
      MapaMesas.jsx              — Visualização de mesas (disponível/ocupada)
      ComandaMesa.jsx            — Detalhe da comanda ativa de uma mesa
    pedidos/
      ListaPedidos.jsx           — Lista geral de pedidos com filtros
  
  components/
    pedido/
      CardPedido.jsx             — Card resumo de pedido
      ModalidadeSelector.jsx     — Seletor BALCAO/MESA/VIAGEM/DELIVERY com ícones
      ItemPedidoRow.jsx          — Linha de item na lista do pedido
      StatusBadge.jsx            — Badge colorido por status
    produto/
      ProdutoCard.jsx            — Card de produto no grid de seleção
      QuantidadeSelector.jsx     — + / - para ajustar quantidade
    cozinha/
      CardPedidoCozinha.jsx      — Card de pedido na fila de preparo
      TimerDecorrido.jsx         — Timer visual de tempo desde abertura
    mesa/
      MesaCard.jsx               — Card de mesa (livre/ocupada/cor)
    common/
      IconButton.jsx             — Botão com ícone (padrão UX da app)
      ConfirmDialog.jsx          — Modal de confirmação
      Header.jsx                 — Cabeçalho com perfil e navegação

  hooks/
    usePedidos.js                — Hook para CRUD de pedidos via API
    useComandas.js               — Hook para operações de comanda
    useCozinha.js                — Hook para fila de preparo (polling/SSE)
    useMesas.js                  — Hook para status das mesas

  services/
    api.js                       — Axios instance configurado
    pedidoService.js             — Chamadas REST para pedidos
    comandaService.js            — Chamadas REST para comandas
    cozinhaService.js            — Chamadas REST para fila de cozinha

  store/
    pedidoStore.js               — Zustand store para estado local de pedidos
```

### 4.3 Fluxo de Telas — Novo Pedido (UX)

```
[Tela Principal]
    ↓
[1] Seleção de Modalidade (4 ícones grandes: 🏪 Balcão, 🪑 Mesa, 🛍️ Viagem, 🛵 Delivery)
    ↓
[2] Se MESA → Selecionar mesa no mapa visual → auto-abre comanda ou usa existente
    Se DELIVERY → Campo de plataforma (dropdown: iFood, Rappi, Entrega própria)
    ↓
[3] Grid de Produtos (cards com imagem/ícone + nome + preço, organizados por categoria)
    → Toque/clique adiciona ao pedido → +/- ajusta quantidade
    → Observação por item (campo opcional, abre ao tocar no item)
    ↓
[4] Resumo do Pedido (itens + total + observação geral)
    → Botão "Confirmar" envia para a fila de preparo
```

**Meta UX (CA-01)**: Pedido de balcão com 3 itens em ≤ 5 cliques.
- Clique 1: Modalidade BALCAO
- Cliques 2-4: 3 produtos no grid
- Clique 5: Confirmar

### 4.4 Fila de Cozinha — Atualização em Tempo Real

**Decisão**: Polling com intervalo de **5 segundos** via `setInterval` + `GET /api/v1/cozinha/fila`.

**Justificativa**: Simplicidade. SSE ou WebSocket são overkill para volume de uma padaria. Se necessário escalar, migrar para SSE é incremental.

### 4.5 Mapa de Mesas

- Grid visual com cards representando cada mesa.
- Cores: Verde (livre), Laranja (comanda aberta), Vermelho (comanda fechada aguardando pagamento).
- Clique na mesa → abre comanda ativa ou oferece criar nova.

---

## 5. Segurança e Perfis de Acesso

### 5.1 Perfis (Spring Security)

| Perfil | Permissões neste módulo |
|---|---|
| `ATENDENTE` | Criar pedido, adicionar/remover itens, marcar ENTREGUE |
| `COZINHA` | Visualizar fila, marcar EM_PREPARO, marcar PRONTO |
| `CAIXA` | Fechar comanda, marcar FINALIZADO, cancelar pedido |
| `GERENTE` | Tudo + cancelar pedido + visualizar todos os pedidos |

### 5.2 Implementação

- `@PreAuthorize` nos controllers.
- JWT token com roles.
- Frontend condiciona exibição de ações por perfil (role no token).

---

## 6. Auditoria (RNF04)

### Decisão: Colunas de auditoria + Event Log

- Cada entidade: `criado_em`, `atualizado_em`, `criado_por`, `atualizado_por` (via JPA `@EntityListeners`).
- Tabela `evento_auditoria` para ações críticas:

| Coluna | Tipo | Descrição |
|---|---|---|
| `id` | `BIGSERIAL` | PK |
| `entidade` | `VARCHAR(50)` | Ex.: `pedido`, `comanda` |
| `entidade_id` | `BIGINT` | ID do registro afetado |
| `acao` | `VARCHAR(50)` | Ex.: `CANCELADO`, `ITEM_REMOVIDO` |
| `dados` | `JSONB` | Payload antes/depois |
| `usuario_id` | `BIGINT` | Quem executou |
| `data_hora` | `TIMESTAMPTZ` | Quando |

---

## 7. Parametrização (RN-ATD-09, RN-ATD-10)

| Parâmetro | Chave | Default | Descrição |
|---|---|---|---|
| Tempo de exibição de PRONTO na cozinha | `cozinha.tempo_exibicao_pronto_min` | 10 | Minutos que o pedido PRONTO fica visível |
| Reinício do número diário | `pedido.reinicio_numero_diario` | `true` | Se o número sequencial reinicia diariamente |

Armazenados em tabela `parametro` (chave/valor) consultada por service.

---

## 8. Tratamento de Erros — Códigos

| Código | HTTP | Quando |
|---|---|---|
| `PEDIDO_NAO_ENCONTRADO` | 404 | ID de pedido inexistente |
| `TRANSICAO_STATUS_INVALIDA` | 400 | Transição de status não permitida |
| `MESA_COM_COMANDA_ABERTA` | 409 | Tentativa de abrir segunda comanda em mesa ocupada |
| `PRODUTO_INDISPONIVEL` | 422 | Produto desativado ou sem estoque |
| `CANCELAMENTO_SEM_MOTIVO` | 400 | Cancelamento sem motivo preenchido |
| `ITEM_PEDIDO_BLOQUEADO` | 409 | Remoção de item em pedido com status avançado sem justificativa |

---

## 9. Decisões Técnicas Consolidadas

| # | Decisão | Justificativa | Referência SPEC |
|---|---|---|---|
| DT-01 | PostgreSQL `BIGSERIAL` para PKs | Simplicidade + performance para volume de padaria | §8 |
| DT-02 | Enums como `VARCHAR` com CHECK (não tipo ENUM nativo PG) | Facilidade de migração e adição de valores | §5, §6 |
| DT-03 | Snapshot de preço em `item_pedido.preco_unitario` | Integridade financeira (RN-ATD-02) | §8, §9 |
| DT-04 | Unique index parcial para comanda aberta por mesa | Garantia em nível de banco (RN-ATD-04) | §6 |
| DT-05 | Número do pedido via MAX+1 em transação | Simplicidade; escala suficiente para padaria | §9, RN-ATD-08 |
| DT-06 | Polling 5s para fila de cozinha | Simplicidade; SSE como upgrade futuro | §7 |
| DT-07 | Zustand para state management React | Leve, sem boilerplate; adequado ao escopo | §4.1 |
| DT-08 | JWT com roles para autorização | Padrão Spring Security + perfis da padaria | §5 |
| DT-09 | Event log JSONB para auditoria | Flexível, evita muitas tabelas de log | §6 (audit) |
| DT-10 | Tabela `parametro` chave/valor para configurações | Simple key-value suficiente para o volume | §7, RN-ATD-09/10 |

---

## 10. Premissas Técnicas (podem mudar)

| # | Premissa | Impacto se mudar |
|---|---|---|
| PT-01 | Backend e frontend na mesma rede local (sem CDN) | Se deploy separado, configurar CORS |
| PT-02 | Único servidor de aplicação (sem cluster) | Se escalar, tratar sequência de pedido com lock distribuído |
| PT-03 | Sem cache externo (Redis) na v1 | Se performance insuficiente, adicionar cache para consultas de produto |
| PT-04 | Sem mensageria (Kafka/RabbitMQ) na v1 | Se módulos desacoplados, notificações via evento assíncrono |

---

## 11. Interfaces com Módulos Adjacentes — Contratos Preliminares

> Contratos completos serão definidos nas SPECs/PLANs dos respectivos módulos. Aqui define-se apenas o que **este módulo consome ou produz**.

### 11.1 Consumo do Módulo 1 (Cadastros)

```
GET /api/v1/produtos?disponivel=true&categoriaId={id}
→ List<ProdutoResponse> { id, nome, preco, categoriaId, categoriaNome, imagemUrl, disponivel }

GET /api/v1/mesas
→ List<MesaResponse> { id, numero, capacidade, status }
```

### 11.2 Produção para Módulo 4 (Caixa/Financeiro)

Ao finalizar pedido ou fechar comanda, este módulo **não chama diretamente** o Módulo 4. O Módulo 4 consulta dados deste módulo. Desacoplamento via leitura:

```
GET /api/v1/pedidos/{id}        → dados completos para registro de pagamento
GET /api/v1/comandas/{id}       → dados consolidados da comanda
```

### 11.3 Notificação para Módulo 2 (Estoque)

**Decisão v1**: sem integração automática. Baixa de estoque será funcionalidade do Módulo 2, trigger manual ou batch.

---

## 12. Rastreabilidade SPEC → PLAN

| Requisito SPEC | Decisão PLAN |
|---|---|
| RF07 (venda balcão) | Endpoint POST /pedidos + modalidade BALCAO |
| RF08 (mesa) | Endpoint POST /pedidos + comanda + mesa_id |
| RF09 (viagem/delivery) | Endpoint POST /pedidos + modalidade VIAGEM/DELIVERY + plataforma |
| RF10 (comandas) | Endpoint POST/PATCH /comandas + unique index mesa |
| RF18 (fila preparo) | Endpoint GET /cozinha/fila + polling 5s |
| RN-ATD-01 (mín. 1 item) | @Size(min=1) no DTO |
| RN-ATD-02 (snapshot preço) | Campo preco_unitario em item_pedido |
| RN-ATD-03 (cancela + motivo) | CHECK no banco + validação DTO |
| RN-ATD-04 (1 comanda/mesa) | Unique index parcial |
| RN-ATD-05 (plataforma delivery) | CHECK no banco + validação condicional |
| RN-ATD-06 (add itens ABERTO/EM_PREPARO) | Validação no service |
| RN-ATD-07 (remoção + justificativa) | Validação condicional + event log |
| RN-ATD-08 (número diário) | MAX+1 em transação |
| RN-ATD-09 (parametrização) | Tabela parametro |
| RN-ATD-10 (tempo PRONTO parametrizável) | Tabela parametro |
| CA-01 (≤5 cliques) | UX: modalidade → produtos → confirmar |
| CA-08 (interface legível) | React Icons, cards grandes, linguagem cotidiana |
