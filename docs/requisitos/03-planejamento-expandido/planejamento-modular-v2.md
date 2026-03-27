# Planejamento Modular Expandido v2 — Sistema Padaria Completo

> **Status**: Planejamento aprovado para especificação  
> **Data**: 2026-03-26  
> **Origem**: Revisão completa dos documentos de requisitos + expansão para sistema robusto e detalhado  
> **Stack**: React + Java Spring Boot + PostgreSQL  
> **Premissa de uso**: PWA responsivo (desktop + mobile), rede local + internet

---

## 1. Motivação da Expansão

O escopo original (6 módulos) cobria o essencial, mas o sistema precisa ser **robusto e completo** para realmente resolver os problemas de uma padaria real. Esta versão expande os requisitos incorporando:

- **Segurança e controle de acesso** granular (login, permissões, auditoria)
- **Controle de validades** de produtos e insumos perecíveis
- **Controle de pragas** e conformidade sanitária
- **Gestão de fornecedores** com avaliação e histórico
- **Contas a pagar e receber** simplificado
- **Controle financeiro** integrado (fluxo de caixa, DRE simplificado)
- **Produção e fichas técnicas** de receitas
- **Sistematização móvel** (PWA, responsivo, offline básico)
- **Mecanismos de automação e alertas** para dinamizar o controle geral

---

## 2. Estrutura Modular Expandida — Visão Geral

| # | Módulo | Foco Principal | Prioridade |
|---|---|---|---|
| **M00** | Segurança e Acesso | Login, perfis, permissões, auditoria, sessões | **CRÍTICA** |
| **M01** | Cadastros Gerais | Produtos, ingredientes, categorias, mesas, unidades | ALTA |
| **M02** | Cadastro de Pessoas | Clientes, funcionários, fornecedores | ALTA |
| **M03** | Estoque e Controle de Validade | Entradas, saídas, lotes, validade, PVPS, alertas | ALTA |
| **M04** | Compras e Fornecedores | Cotação, pedido de compra, recebimento, avaliação | MÉDIA-ALTA |
| **M05** | Produção e Receitas | Fichas técnicas, ordens de produção, consumo automático | MÉDIA |
| **M06** | Atendimento e Pedidos | Balcão, mesa, comanda, viagem, delivery, fila de preparo | **CRÍTICA** |
| **M07** | Caixa e Pagamentos | Abertura/fechamento, formas de pagamento, sangria, suprimento | **CRÍTICA** |
| **M08** | Financeiro Básico | Contas a pagar, contas a receber, fiado, vales, fluxo de caixa | ALTA |
| **M09** | Controle de Pessoal | Ponto simplificado, escalas, ocorrências, vales de funcionário | MÉDIA |
| **M10** | Higiene, Limpeza e Controle de Pragas | Checklists, rotinas sanitárias, registros de dedetização, APPCC básico | MÉDIA |
| **M11** | Manutenção e Equipamentos | Cadastro de equipamentos, preventiva/corretiva, alertas | MÉDIA |
| **M12** | Relatórios e Dashboards | Vendas, estoque, financeiro, operacional, gerencial | ALTA |
| **M13** | Configurações e Parametrização | Parâmetros do sistema, dados da empresa, integrações | MÉDIA |
| **M14** | Mobile e Acessibilidade | PWA, responsividade, modo offline básico, usabilidade | **CRÍTICA** |

---

## 3. Detalhamento por Módulo

---

### M00 — Segurança e Acesso

**Objetivo**: Garantir que cada pessoa acesse somente o que seu perfil permite, com rastreabilidade total de ações.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-SEC-01 | Autenticação por usuário e senha com hash seguro (bcrypt/argon2) |
| RF-SEC-02 | Gerenciamento de perfis de acesso: Dono, Gerente, Caixa, Atendente, Chapeiro/Cozinha, Administrativo |
| RF-SEC-03 | Atribuição de permissões por perfil: leitura, escrita, exclusão, aprovação por módulo/tela |
| RF-SEC-04 | Registro de log de auditoria: toda ação relevante grava usuário, data/hora, IP, ação, entidade afetada |
| RF-SEC-05 | Controle de sessão: timeout por inatividade configurável, logout automático |
| RF-SEC-06 | Bloqueio de conta após N tentativas de login incorretas |
| RF-SEC-07 | Troca de senha obrigatória no primeiro acesso |
| RF-SEC-08 | Recuperação de senha simplificada (reset pelo administrador / dono) |
| RF-SEC-09 | Dashboard de sessões ativas (para dono/gerente verificar quem está logado) |
| RF-SEC-10 | Modo "troca rápida de usuário" para dispositivos compartilhados (PIN rápido) |

#### Perfis Padrão

| Perfil | Acesso Principal |
|---|---|
| **Dono** | Acesso total. Único que gerencia usuários e vê financeiro completo. |
| **Gerente** | Tudo exceto gestão de usuários e configurações sensíveis. |
| **Caixa** | Caixa, pagamentos, fiado, consulta de produtos/preços. |
| **Atendente** | Pedidos, comandas, consulta de cardápio/estoque. |
| **Chapeiro/Cozinha** | Fila de preparo (somente leitura e atualização de status). |
| **Administrativo** | Estoque, compras, fornecedores, relatórios operacionais. |

---

### M01 — Cadastros Gerais

**Objetivo**: Base de dados mestre para todo o sistema. Produtos, ingredientes, categorias, mesas, unidades de medida.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-CAD-01 | Cadastrar produtos vendidos: descrição, categoria, preço de venda, unidade, foto opcional, situação (ativo/inativo), código de barras opcional |
| RF-CAD-02 | Cadastrar ingredientes/insumos: descrição, categoria, unidade de medida, estoque mínimo, perecível (S/N), dias de validade padrão |
| RF-CAD-03 | Cadastrar categorias de produtos e de insumos (hierarquia simples: grupo → sub-grupo) |
| RF-CAD-04 | Cadastrar mesas: número, capacidade, localização (salão, calçada), situação (disponível/ocupada/reservada/inativa) |
| RF-CAD-05 | Cadastrar unidades de medida: kg, g, L, mL, unidade, pacote, caixa, dúzia |
| RF-CAD-06 | Cadastrar formas de pagamento aceitas: dinheiro, cartão débito/crédito, PIX, fiado, vale-refeição |
| RF-CAD-07 | Manter histórico de alteração de preço de produto (data, preço anterior, preço novo, quem alterou) |
| RF-CAD-08 | Cadastrar materiais de apoio e limpeza: sacolas, embalagens, detergente, álcool, papel toalha |
| RF-CAD-09 | Importação/cadastro em lote de produtos via planilha CSV (facilitar carga inicial) |
| RF-CAD-10 | Busca inteligente de produtos: por nome parcial, categoria, código de barras |

---

### M02 — Cadastro de Pessoas

**Objetivo**: Centralizar cadastro de clientes, funcionários e fornecedores com informações relevantes para cada papel.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-PES-01 | Cadastrar clientes: nome, apelido, telefone/WhatsApp, endereço simplificado, observações, situação, flag "compra fiado" (S/N) |
| RF-PES-02 | Cadastrar funcionários: nome, função, data admissão, salário base, telefone, situação (ativo/afastado/desligado), foto opcional |
| RF-PES-03 | Cadastrar fornecedores: razão social, nome fantasia, CNPJ/CPF, telefone, e-mail, endereço, produtos que fornece, prazo de entrega médio, condição de pagamento |
| RF-PES-04 | Vincular fornecedores aos insumos/produtos que fornecem (relação N:N) |
| RF-PES-05 | Classificar fornecedores com avaliação simples (1-5 estrelas) baseada em experiência |
| RF-PES-06 | Histórico de interações com fornecedor: última compra, prazo cumprido, problemas registrados |
| RF-PES-07 | Busca rápida de cliente por nome/apelido/telefone (para atendimento ágil no fiado) |
| RF-PES-08 | Registrar dependentes/autorizados para retirada fiado (ex.: "esposa do João pode pegar fiado") |

---

### M03 — Estoque e Controle de Validade

**Objetivo**: Controlar todo o fluxo de materiais com rastreabilidade de lotes, validades e alertas inteligentes.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-EST-01 | Registrar entrada de estoque: item, quantidade, lote (opcional), data de validade, fornecedor, nota/recibo, responsável |
| RF-EST-02 | Registrar saída de estoque: por venda (automática), por produção (ficha técnica), por perda/descarte, por transferência interna |
| RF-EST-03 | Consultar saldo atual por item: quantidade disponível, última entrada, última saída |
| RF-EST-04 | Alertas de estoque mínimo: notificação visual quando item atinge nível mínimo configurado |
| RF-EST-05 | **Controle de validade por lote**: registrar data de validade na entrada, exibir itens próximos do vencimento |
| RF-EST-06 | **Alerta de vencimento**: notificação com antecedência configurável (ex.: 3 dias antes) para itens perecíveis |
| RF-EST-07 | **PVPS (Primeiro que Vence, Primeiro que Sai)**: sugerir automaticamente saída pelo lote mais próximo do vencimento |
| RF-EST-08 | Registrar perdas e desperdícios: motivo (vencimento, queda, preparo incorreto, deterioração), quantidade, valor estimado, responsável |
| RF-EST-09 | Registrar descarte formal com motivo e assinatura digital simplificada (confirmação no sistema) |
| RF-EST-10 | Inventário/contagem física: tela para lançamento de contagem real vs. saldo sistema, com apuração de diferenças |
| RF-EST-11 | Classificação ABC automática dos itens (por valor de consumo) para priorização |
| RF-EST-12 | Sugestão automática de compra: itens abaixo do mínimo ou com previsão de falta baseada no consumo médio |
| RF-EST-13 | Controle de estoque separado: insumos de produção, produtos prontos para venda, materiais de apoio/limpeza |

---

### M04 — Compras e Fornecedores

**Objetivo**: Organizar o processo de compra desde a necessidade até o recebimento, com histórico e comparação de preços.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-CMP-01 | Gerar lista de compra automática a partir de itens abaixo do estoque mínimo ou sugestão do sistema |
| RF-CMP-02 | Criar pedido de compra: fornecedor, itens, quantidades, preço negociado, prazo de entrega, condição de pagamento |
| RF-CMP-03 | Registrar recebimento de compra: conferência item a item, quantidade recebida vs. pedida, datas de validade dos lotes |
| RF-CMP-04 | Registrar divergências no recebimento: item faltante, quantidade diferente, produto danificado, validade curta |
| RF-CMP-05 | Histórico de preços por item/fornecedor: comparar evolução de preços ao longo do tempo |
| RF-CMP-06 | Comparação de fornecedores: mesmo item, preços diferentes, prazos, avaliações |
| RF-CMP-07 | Gerar conta a pagar automaticamente ao confirmar recebimento (integração com M08) |
| RF-CMP-08 | Relatório de compras por período, fornecedor, categoria de insumo |

---

### M05 — Produção e Receitas

**Objetivo**: Controlar o que é produzido na padaria, com fichas técnicas que permitam calcular custo, consumir estoque automaticamente e planejar produção.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-PRD-01 | Cadastrar ficha técnica (receita): produto final, lista de ingredientes com quantidade por unidade produzida, modo de preparo simplificado |
| RF-PRD-02 | Calcular custo unitário do produto a partir da ficha técnica (soma dos insumos + margem configurável) |
| RF-PRD-03 | Registrar ordem de produção: o que produzir, quantidade planejada, quantidade real produzida, perdas |
| RF-PRD-04 | Baixa automática de estoque de insumos ao confirmar produção (consumo baseado na ficha técnica × quantidade produzida) |
| RF-PRD-05 | Entrada automática no estoque de produtos prontos após produção |
| RF-PRD-06 | Registrar perdas de produção: produto queimado, massa que não cresceu, descarte pós-preparo |
| RF-PRD-07 | Histórico de produção: o que foi produzido, quando, por quem, quanto custou |
| RF-PRD-08 | Sugestão de produção baseada em histórico de vendas (média dos últimos X dias por produto) |
| RF-PRD-09 | Alerta de insumo insuficiente antes de iniciar produção (verificar estoque vs. ficha técnica) |

---

### M06 — Atendimento e Pedidos *(já possui SPEC-001 em rascunho)*

**Objetivo**: Registrar e acompanhar pedidos de todas as modalidades com visibilidade para preparo.

#### Requisitos Funcionais (revisados e expandidos)

| Código | Descrição |
|---|---|
| RF-ATD-01 | Registrar pedidos por modalidade: balcão, mesa, viagem, delivery |
| RF-ATD-02 | Controlar comandas vinculadas a mesas |
| RF-ATD-03 | Fila de preparo para cozinha/chapa com estados: aguardando → em preparo → pronto |
| RF-ATD-04 | Adicionar/remover itens de pedido aberto (com justificativa se em preparo) |
| RF-ATD-05 | Cancelamento de pedido com motivo obrigatório e auditoria |
| RF-ATD-06 | Identificação visual de modalidade (ícones/cores) para agilidade |
| RF-ATD-07 | Separação de pedidos delivery por plataforma (iFood, entrega própria, etc.) para análise de custo |
| RF-ATD-08 | **Tempo médio de preparo por pedido** (métricas operacionais) |
| RF-ATD-09 | **Notificação sonora/visual** quando pedido fica pronto (para balcão e mesa) |
| RF-ATD-10 | **Cardápio digital integrado**: lista de produtos disponíveis com status de disponibilidade em tempo real |
| RF-ATD-11 | **Observações por item** ("sem cebola", "bem passado", "pouco sal") — visíveis na fila de preparo |
| RF-ATD-12 | **Histórico de pedidos por cliente** (para clientes cadastrados) |

---

### M07 — Caixa e Pagamentos

**Objetivo**: Controlar toda a movimentação financeira do dia com abertura/fechamento formal de caixa.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-CX-01 | Abertura de caixa: valor de abertura (troco), operador responsável, data/hora |
| RF-CX-02 | Registro de pagamento de pedidos: dinheiro, cartão (débito/crédito), PIX, fiado, vale-refeição, misto |
| RF-CX-03 | Pagamento misto: permitir dividir pagamento em mais de uma forma (ex.: parte PIX + parte dinheiro) |
| RF-CX-04 | Sangria de caixa: retirada de dinheiro com motivo, valor, responsável, autorização (dono/gerente) |
| RF-CX-05 | Suprimento de caixa: entrada de dinheiro adicional no caixa |
| RF-CX-06 | Registro de vale de funcionário pelo caixa (integração com M09) |
| RF-CX-07 | Fechamento de caixa: totalização por forma de pagamento, valor esperado vs. valor contado, diferença |
| RF-CX-08 | Conferência cega: operador informa valor contado ANTES de ver o valor esperado pelo sistema |
| RF-CX-09 | Relatório de movimentação do caixa do dia: entradas, saídas, sangrias, suprimentos, formas de pagamento |
| RF-CX-10 | Histórico de caixas: consultar fechamentos anteriores com detalhamento |
| RF-CX-11 | **Troco calculado automaticamente** quando pagamento em dinheiro |
| RF-CX-12 | **Gaveta do caixa**: saldo atual estimado de dinheiro em caixa |

---

### M08 — Financeiro Básico

**Objetivo**: Visão financeira simplificada mas eficaz: contas a pagar, contas a receber, fiado, fluxo de caixa.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-FIN-01 | **Contas a pagar**: registrar débito com fornecedor/prestador — valor, vencimento, forma pagamento, status (pendente/pago/atrasado) |
| RF-FIN-02 | **Contas a receber**: registrar crédito de clientes fiado, cheques pré-datados, vendas a prazo |
| RF-FIN-03 | Baixa de conta a pagar: registro de pagamento efetuado com data, valor pago, forma |
| RF-FIN-04 | Baixa de conta a receber: registro de recebimento com data, valor, forma |
| RF-FIN-05 | **Gestão de fiado completa**: registro de crédito por cliente, pagamentos parciais, saldo devedor, histórico |
| RF-FIN-06 | Alerta de contas a pagar próximas do vencimento (com antecedência configurável) |
| RF-FIN-07 | Alerta de fiados em atraso (com faixas: 7 dias, 15 dias, 30+ dias) |
| RF-FIN-08 | **Fluxo de caixa simplificado**: visão de entradas e saídas previstas e realizadas por período |
| RF-FIN-09 | **DRE simplificado**: receitas, custos de mercadoria, despesas operacionais, resultado do período |
| RF-FIN-10 | Categorização de despesas: matéria-prima, manutenção, limpeza, taxa delivery, salários, aluguel, água, luz, gás |
| RF-FIN-11 | Registro de despesas recorrentes: aluguel, energia, gás, água (lançamento automático mensal) |
| RF-FIN-12 | **Análise de custo delivery**: comparar receita vs. taxas/embalagem/custos por plataforma |
| RF-FIN-13 | **Vales de funcionário como despesa**: integrar vales do M09 automaticamente como saída financeira |
| RF-FIN-14 | Conciliação de caixa vs. contas: cruzar fechamento de caixa com movimentação financeira |
| RF-FIN-15 | **Emissão de boleto bancário (Bradesco)** para cobrança de fiado: individual ou consolidado mensal por cliente |
| RF-FIN-16 | **Baixa automática de boleto** via arquivo de retorno CNAB 400 do Bradesco |
| RF-FIN-17 | Impressão de boleto: formato padrão FEBRABAN com código de barras e linha digitável |
| RF-FIN-18 | Geração em lote de boletos: selecionar múltiplos clientes com saldo devedor e gerar boletos de uma vez |
| RF-FIN-19 | Consulta de situação dos boletos: gerado, registrado, pago, vencido, cancelado |

---

### M09 — Controle de Pessoal

**Objetivo**: Gestão simplificada de funcionários, ponto, escalas, vales e ocorrências.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-RH-01 | Registro de ponto simplificado: entrada e saída, com horário e dispositivo |
| RF-RH-02 | Correção de ponto com justificativa obrigatória e aprovação do dono/gerente |
| RF-RH-03 | Registro de vale para funcionário: valor, data, motivo, quem autorizou, vinculação ao caixa |
| RF-RH-04 | Resumo mensal por funcionário: dias trabalhados, horas totais, atrasos, vales acumulados, saldo de vales |
| RF-RH-05 | Cadastro de escalas de trabalho por funcionário (turnos, folgas, dias da semana) |
| RF-RH-06 | Alerta de irregularidades: falta sem justificativa, atraso frequente, excesso de vales |
| RF-RH-07 | Registro de ocorrências: advertência verbal, advertência escrita, elogio, observação |
| RF-RH-08 | Histórico completo do funcionário: admissão, vales, ponto, ocorrências, alterações salariais |
| RF-RH-09 | Relatório de custo de pessoal: salários + vales + encargos estimados por período |

---

### M10 — Higiene, Limpeza e Controle de Pragas

**Objetivo**: Manter registros sanitários, checklists de limpeza e conformidade básica com vigilância sanitária.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-HIG-01 | **Checklists de limpeza** por área e período: salão, mesas, banheiro, cozinha, área de produção, calçada |
| RF-HIG-02 | Registro de execução do checklist: quem fez, horário, observações, conformidade (OK/pendência) |
| RF-HIG-03 | **Controle de pragas**: registro de dedetizações (data, empresa, tipo de tratamento, áreas tratadas, validade do serviço) |
| RF-HIG-04 | Alerta de vencimento de dedetização (lembrete com antecedência configurável) |
| RF-HIG-05 | Registro de inspeção sanitária: data, inspetor, resultado, não-conformidades, prazo de correção |
| RF-HIG-06 | **Checklist de boas práticas (APPCC básico)**: temperatura de equipamentos, higiene pessoal, armazenamento |
| RF-HIG-07 | Registro de temperatura de equipamentos: geladeiras, freezers, estufas (manual com horário) |
| RF-HIG-08 | Controle de materiais de limpeza: estoque mínimo integrado com M03 |
| RF-HIG-09 | Alerta de rotinas de limpeza não executadas no prazo |
| RF-HIG-10 | Relatório de conformidade sanitária por período (para apresentar em fiscalizações) |

---

### M11 — Manutenção e Equipamentos

**Objetivo**: Cadastrar equipamentos e manter rotinas de manutenção preventiva, evitando paradas inesperadas.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-MAN-01 | Cadastrar equipamentos: descrição, marca, modelo, localização, data de aquisição, valor, foto opcional |
| RF-MAN-02 | Registrar manutenção preventiva: tipo (aferição balança, troca filtro, afiação corta-frio), periodicidade, última execução |
| RF-MAN-03 | Registrar manutenção corretiva: equipamento, problema, data, técnico, custo, tempo parado |
| RF-MAN-04 | Alerta de manutenção preventiva pendente (com antecedência configurável) |
| RF-MAN-05 | Histórico de manutenção por equipamento: todas as preventivas e corretivas |
| RF-MAN-06 | Controle de vida útil: alertar quando equipamento atinge X anos ou X manutenções corretivas |
| RF-MAN-07 | Relatório de custos de manutenção por período e por equipamento |
| RF-MAN-08 | Registro de fornecedores/técnicos de manutenção (integração com M02) |

---

### M12 — Relatórios e Dashboards

**Objetivo**: Dar ao dono/gerente visão clara e acionável de todo o negócio.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-REL-01 | **Dashboard principal** (tela inicial do dono): vendas do dia, ticket médio, caixa atual, alertas pendentes, itens em falta |
| RF-REL-02 | Relatório de vendas: por período, modalidade, produto, categoria, hora do dia, dia da semana |
| RF-REL-03 | Relatório de estoque: posição atual, itens críticos, vencimentos próximos, curva ABC |
| RF-REL-04 | Relatório financeiro: fluxo de caixa, contas a pagar/receber, DRE simplificado |
| RF-REL-05 | Relatório de fiado: clientes devedores, valores, atrasos, histórico de pagamentos |
| RF-REL-06 | Relatório de funcionários: ponto, vales, custo de pessoal |
| RF-REL-07 | Relatório de compras: por fornecedor, item, período, evolução de preços |
| RF-REL-08 | Relatório de perdas e desperdício: por motivo, período, valor estimado |
| RF-REL-09 | Relatório de produção: produzido vs. vendido, custo por produto, rendimento |
| RF-REL-10 | Relatório de delivery: vendas por plataforma, custos, lucratividade por canal |
| RF-REL-11 | Relatório de higiene e manutenção: conformidade, pendências, custos |
| RF-REL-12 | **Indicadores / KPIs no dashboard**: margem bruta, percentual de desperdício, ticket médio, vendas/hora, taxa de ocupação de mesas |
| RF-REL-13 | Exportação de relatórios em PDF e CSV |
| RF-REL-14 | Filtros flexíveis: data, período predefinido (hoje, semana, mês, trimestre), categoria, funcionário |
| RF-REL-15 | Comparativo de períodos: este mês vs. mês anterior, esta semana vs. semana anterior |

---

### M13 — Configurações e Parametrização

**Objetivo**: Centralizar parâmetros do sistema para que o dono customize o comportamento sem alterar código.

#### Requisitos Funcionais

| Código | Descrição |
|---|---|
| RF-CFG-01 | Dados da empresa: nome, CNPJ, endereço, telefone, logo (para impressão/relatórios) |
| RF-CFG-02 | Parâmetros de estoque: dias de antecedência para alerta de validade, percentual de estoque mínimo |
| RF-CFG-03 | Parâmetros de caixa: valor padrão de troco na abertura, limite de sangria sem aprovação |
| RF-CFG-04 | Parâmetros de segurança: tempo de timeout de sessão, tentativas de login, política de senha |
| RF-CFG-05 | Parâmetros de fiado: limite de crédito padrão, dias para considerar atraso, faixas de alerta |
| RF-CFG-06 | Parâmetros de manutenção: periodicidade padrão por tipo de equipamento |
| RF-CFG-07 | Parâmetros de higiene: horários de checklists obrigatórios, áreas cadastradas |
| RF-CFG-08 | Parâmetros de relatórios: período padrão, logotipo em impressão |
| RF-CFG-09 | Horário de funcionamento da padaria (para cálculos de ponto e operação) |
| RF-CFG-10 | Taxas de delivery por plataforma (para cálculo de custo no M08) |
| RF-CFG-11 | Backup: rotina de backup agendável com alerta de status |

---

### M14 — Mobile e Acessibilidade

**Objetivo**: Garantir que o sistema funcione bem em qualquer dispositivo, com foco em usabilidade para pessoas com pouca experiência digital.

#### Requisitos Funcionais e Não Funcionais

| Código | Descrição |
|---|---|
| RF-MOB-01 | **PWA (Progressive Web App)**: instalável no celular e tablet como app, sem loja |
| RF-MOB-02 | **Design responsivo**: todas as telas adaptadas para desktop, tablet e smartphone |
| RF-MOB-03 | **Modo offline básico**: caixa, consulta de preços e registro de pedidos funcionam sem internet (sincroniza ao reconectar) |
| RF-MOB-04 | Telas com fonte grande, botões amplos, navegação por toque |
| RF-MOB-05 | Atalhos rápidos na tela inicial por perfil (atendente vê pedidos, caixa vê caixa, cozinha vê fila) |
| RF-MOB-06 | Notificações push para alertas críticos: estoque mínimo, validade, manutenção, fiado em atraso |
| RF-MOB-07 | Leitura de código de barras via câmera do celular (para busca de produto e conferência de estoque) |
| RF-MOB-08 | Feedback visual e sonoro: confirmação de ações, alertas, notificações |
| RF-MOB-09 | Linguagem cotidiana da padaria em todos os rótulos, mensagens e instruções |
| RF-MOB-10 | Acessibilidade básica: contraste adequado, textos legíveis, sem dependência exclusiva de cor |

---

## 4. Mapa de Integrações entre Módulos

```
M00 (Segurança) ─────────────── permeia TODOS os módulos
       │
M01 (Cadastros) ◄──────────── base para M03, M04, M05, M06, M07
       │
M02 (Pessoas) ◄────────────── base para M06, M07, M08, M09, M10
       │
M03 (Estoque) ◄───► M04 (Compras): entrada automática no recebimento
       │         ◄───► M05 (Produção): baixa automática por ficha técnica
       │         ◄───► M06 (Pedidos): baixa por venda (se configurado)
       │
M05 (Produção) ────► M01 (produto pronto) + M03 (consumo insumo)
       │
M06 (Pedidos) ─────► M07 (Caixa): pagamento
       │
M07 (Caixa) ───────► M08 (Financeiro): receitas do dia
       │           ──► M09 (Pessoal): vales pelo caixa
       │
M08 (Financeiro) ◄── M04 (Compras): contas a pagar
       │           ◄── M07 (Caixa): receitas
       │           ◄── M09 (Pessoal): custo de pessoal
       │
M10 (Higiene) ─────► M03 (Estoque): materiais de limpeza
M11 (Manutenção) ──► M08 (Financeiro): custos de manutenção
       │
M12 (Relatórios) ◄── consulta dados de TODOS os módulos
M13 (Config) ──────── parametriza TODOS os módulos
M14 (Mobile) ──────── camada transversal de UX/responsividade
```

---

## 5. Fases de Implementação Sugeridas

### Fase 1 — Fundação (Pré-requisitos para tudo)
| Ordem | Módulo | Justificativa |
|---|---|---|
| 1.1 | **M00** — Segurança e Acesso | Sem login e perfis, nada funciona de forma segura |
| 1.2 | **M14** — Mobile e Acessibilidade | Arquitetura PWA/responsiva desde o início, não como remendo |
| 1.3 | **M13** — Configurações | Parametrização base para todos os módulos |
| 1.4 | **M01** — Cadastros Gerais | Base de dados mestre |
| 1.5 | **M02** — Cadastro de Pessoas | Clientes, funcionários, fornecedores |

### Fase 2 — Operação Core (O dia a dia da padaria)
| Ordem | Módulo | Justificativa |
|---|---|---|
| 2.1 | **M06** — Atendimento e Pedidos | Fluxo principal: vender |
| 2.2 | **M07** — Caixa e Pagamentos | Receber pagamentos, fechar caixa |
| 2.3 | **M03** — Estoque e Validade | Controlar o que entra e sai |

### Fase 3 — Gestão e Controle
| Ordem | Módulo | Justificativa |
|---|---|---|
| 3.1 | **M04** — Compras e Fornecedores | Organizar compras com base no estoque |
| 3.2 | **M05** — Produção e Receitas | Fichas técnicas, custeio, baixa automática |
| 3.3 | **M08** — Financeiro Básico | Contas, fiado, fluxo de caixa |

### Fase 4 — Controles Complementares
| Ordem | Módulo | Justificativa |
|---|---|---|
| 4.1 | **M09** — Controle de Pessoal | Ponto, vales, escalas |
| 4.2 | **M10** — Higiene e Pragas | Checklists, conformidade sanitária |
| 4.3 | **M11** — Manutenção | Equipamentos, preventiva |

### Fase 5 — Inteligência e Visão Gerencial
| Ordem | Módulo | Justificativa |
|---|---|---|
| 5.1 | **M12** — Relatórios e Dashboards | Consolida tudo em visão gerencial |

---

## 6. Resumo Quantitativo

| Métrica | Valor |
|---|---|
| Total de módulos | **15** (M00 a M14) |
| Requisitos funcionais mapeados | **~155** |
| Perfis de acesso | **6** |
| Módulos já com spec (rascunho) | **1** (M06 — Atendimento e Pedidos) |
| Módulos pendentes de spec | **14** |
| Fases de implementação | **5** |

---

## 7. Decisões Pendentes Consolidadas (Herdadas + Novas)

| ID | Descrição | Severidade | Bloqueia |
|---|---|---|---|
| DP-FIADO-001 | Fiado para qualquer pessoa ou apenas cadastrados? | Média | M08 Fiado |
| DP-OFFLINE-001 | Nível de funcionalidade offline? | Média-Alta | M14 PWA |
| DP-COZINHA-001 | Cozinha terá monitor, impressora ou voz? | Média | M06 Fila Preparo |
| DP-COMANDAS-001 | Comandas físicas, digitais ou ambas? | Média | M06 Comandas |
| DP-IFOOD-001 | Calcular custo/lucro das vendas iFood? | Média | M08 Análise Delivery |
| DP-RECEITA-001 | Controlar custo por produto ou apenas vendas totais? | Média | M05 Produção |
| **DP-PRAGAS-001** | Padaria já possui empresa de dedetização contratada? | Baixa | M10 Pragas |
| **DP-APPCC-001** | Implementar APPCC básico ou apenas checklists simples? | Média | M10 Higiene |
| **DP-BARCODE-001** | Produtos terão código de barras ou apenas busca por nome? | Baixa | M01, M14 |
| **DP-PONTO-001** | Ponto será por login no sistema, biometria ou registro manual? | Média | M09 Ponto |

---

## 8. Próximos Passos

1. **Validar este planejamento** com o responsável do projeto
2. **Resolver decisões pendentes** de severidade Alta antes de iniciar specs
3. **Gerar specs detalhadas** na ordem das fases de implementação:
   - Fase 1: M00 → M14 → M13 → M01 → M02
   - Fase 2: M06 (atualizar) → M07 → M03
   - Fase 3: M04 → M05 → M08
   - Fase 4: M09 → M10 → M11
   - Fase 5: M12
4. Cada spec seguirá o padrão já estabelecido em SPEC-001 (objetivo, RFs, atores, estados, regras de negócio, fluxos, dados conceituais)
