# Objetivos e Requisitos Iniciais — Sistema Padaria

> **Fonte**: `docs/padaria_objetivos_requisitos_iniciais.docx`  
> **Status**: Normalizado — Fase 1 concluída  
> **Tipo**: Objetivo do sistema + Requisitos funcionais, não funcionais e operacionais

---

## 1. Premissas Adotadas

- Sistema pensado para padaria pequena ou média, com operação completa, porém sem organização formal de processos.
- Uso principal por pessoas com pouca familiaridade com computador, planilhas e sistemas de gestão.
- Projeto acadêmico: interface em React, backend em Java, persistência em PostgreSQL (orientação do curso).
- Requisitos representam ponto de partida; podem ser refinados após entrevistas e observação da rotina real.

---

## 2. Objetivo Geral do Sistema

Desenvolver um sistema de controle geral para padarias de pequeno porte, capaz de apoiar a administração do negócio, reduzir perdas operacionais e organizar informações que hoje ficam dispersas em cadernos, na memória do proprietário e em rotinas informais do dia a dia.

---

## 3. Objetivos Específicos

| # | Objetivo |
|---|---|
| OE-01 | Controlar estoque de ingredientes, produtos prontos, materiais de apoio e itens de limpeza. |
| OE-02 | Registrar vendas de balcão, mesas, entrega própria e plataformas de delivery. |
| OE-03 | Controlar clientes que compram fiado e facilitar cobrança, baixa de pagamento e histórico. |
| OE-04 | Acompanhar jornada, vales e ocorrências ligadas aos funcionários. |
| OE-05 | Apoiar manutenção e higiene por meio de registros simples e alertas básicos. |
| OE-06 | Dar ao dono visão clara do que entra, sai, falta, sobra e gera prejuízo. |

---

## 4. Escopo Inicial do Sistema

| Faixa de escopo | Descrição |
|---|---|
| **Incluído no escopo inicial** | Estoque, vendas, fiado, caixa, vales, cadastro de funcionários, cadastro de clientes, pedidos internos, pedidos de mesa, controle básico de manutenção e higiene, relatórios gerenciais simples. |
| **Incluído de forma simplificada** | Integração conceitual com iFood/delivery, controle de ponto e controle de produção/cozinha — inicialmente em nível acadêmico e adaptável. |
| **Fora do escopo inicial** | Contabilidade formal, emissão fiscal completa, integração bancária, folha de pagamento completa, BI avançado, automações industriais e integrações externas complexas. |

---

## 5. Requisitos Funcionais Iniciais

| Código | Descrição |
|---|---|
| **RF01** | Cadastrar produtos vendidos pela padaria: descrição, categoria, preço, unidade de venda, situação de disponibilidade. |
| **RF02** | Cadastrar ingredientes e insumos da produção: trigo, sal, fermento, leite, manteiga, açúcar, café. |
| **RF03** | Registrar entrada e saída de estoque: motivo, quantidade, data, responsável pelo lançamento. |
| **RF04** | Consultar itens em falta, abaixo do mínimo e com sobra excessiva. |
| **RF05** | Registrar perdas, desperdícios, vencimentos e descarte de mercadorias. |
| **RF06** | Cadastrar fornecedores e registrar compras de mercadorias e materiais. |
| **RF07** | Registrar vendas de balcão e consolidar itens vendidos em cada atendimento. |
| **RF08** | Registrar pedidos para consumo em mesas: mesa, itens, situação do pedido. |
| **RF09** | Registrar pedidos para viagem e entrega, separando pedidos presenciais de plataformas. |
| **RF10** | Controlar comandas ou pedidos abertos até sua finalização. |
| **RF11** | Registrar pagamentos por dinheiro, cartão, pix e fiado. |
| **RF12** | Manter cadastro de clientes fiado: histórico de débitos, pagamentos e saldo pendente. |
| **RF13** | Registrar retirada de vale para funcionário: data, valor, observação, posterior desconto. |
| **RF14** | Manter cadastro de funcionários: função, situação, dados básicos para controle operacional. |
| **RF15** | Registrar horários de entrada e saída dos funcionários, inclusive correções justificadas. |
| **RF16** | Registrar tarefas/ocorrências de manutenção: aferição de balanças, afiação de corta-frios, troca de filtro. |
| **RF17** | Registrar rotinas de higiene e limpeza: mesas, salão, banheiro, cozinha, descarte de lixo. |
| **RF18** | Identificar pedidos pendentes de preparo para cozinha/chapa: aguardando, em preparo, concluído. |
| **RF19** | Gerar relatórios simples: vendas, estoque, fiado, funcionários, vales, desperdício, custos operacionais. |
| **RF20** | Manter histórico de movimentações para consulta posterior pelo proprietário ou gerente. |

---

## 6. Requisitos Não Funcionais Iniciais

| Código | Descrição |
|---|---|
| **RNF01** | Interface simples, poucos passos por operação, linguagem clara, botões de fácil identificação. |
| **RNF02** | Utilizável por pessoas com pouca familiaridade com computador; legibilidade e navegação objetiva. |
| **RNF03** | Autenticação por usuário e senha, com perfis de acesso por função. |
| **RNF04** | Operações relevantes registradas para auditoria básica: data, hora, usuário responsável. |
| **RNF05** | Persistência em banco PostgreSQL; dados preservados em encerramento normal. |
| **RNF06** | Rotina de backup periódico do banco de dados. |
| **RNF07** | Tempo de resposta compatível com uso em balcão; sem lentidão excessiva. |
| **RNF08** | Evolução futura para múltiplos pontos de atendimento sem reescrita completa. |
| **RNF09** | Mensagens de erro compreensíveis ao usuário, não apenas técnicas. |
| **RNF10** | Organizado em camadas: interface, lógica de negócio, persistência. |
| **RNF11** | Nomes de telas, campos e relatórios refletindo linguagem cotidiana da padaria. |
| **RNF12** | Manutenção acadêmica e expansão futura sem acoplamento excessivo entre módulos. |

---

## 7. Requisitos Operacionais e de Uso

| # | Requisito |
|---|---|
| ROP-01 | Pelo menos um ponto principal de atendimento para lançamento de pedidos, com possibilidade de expansão para caixa, cozinha e administração. |
| ROP-02 | Fluxo do atendente com menor número possível de cliques para abrir, editar e finalizar pedido. |
| ROP-03 | Pedidos para cozinha/chapa visíveis com identificação de prioridade e situação atual. |
| ROP-04 | Comandas com forma simples de abertura e fechamento, evitando perda de controle entre consumo e pagamento. |
| ROP-05 | Registro de fiado formalizado no sistema para substituir/complementar caderno manual. |
| ROP-06 | Rotinas de higiene registradas em formato simples (checklist ou ocorrência). |
| ROP-07 | Operações críticas possíveis mesmo com usuários de pouca alfabetização digital. |
| ROP-08 | Uso predominantemente via navegador, em rede local ou internet. |

---

## 8. Perguntas de Levantamento Ainda Pendentes

| # | Pergunta |
|---|---|
| PL-01 | Quantos computadores ou dispositivos realmente existirão na padaria? |
| PL-02 | Haverá apenas um ponto de atendimento ou mais de um ao mesmo tempo? |
| PL-03 | O pedido será feito no balcão, no caixa, na mesa ou em mais de um local? |
| PL-04 | A cozinha/chapa terá monitor, impressora ou apenas acompanhamento por voz? |
| PL-05 | As comandas serão físicas, digitais ou ambas? |
| PL-06 | O fiado continuará existindo para qualquer cliente ou apenas cadastrados? |
| PL-07 | O dono quer controlar receita por produto ou apenas vendas totais? |
| PL-08 | Será necessário saber o custo aproximado das vendas por iFood? |
| PL-09 | Quem será responsável por registrar limpeza, manutenção e troca de filtro? |
| PL-10 | Os funcionários aceitarão registrar ponto e vales em sistema ou haverá resistência inicial? |

---

## 9. Sugestão de Organização Modular

| Módulo | Conteúdo principal |
|---|---|
| **Módulo 1 – Cadastros** | Clientes, funcionários, fornecedores, produtos, ingredientes, categorias e mesas. |
| **Módulo 2 – Estoque e compras** | Entradas, saídas, perdas, níveis mínimos e compras de insumos e materiais. |
| **Módulo 3 – Atendimento e pedidos** | Balcão, mesas, comandas, viagem, delivery e status de preparo. |
| **Módulo 4 – Caixa e financeiro básico** | Pagamentos, fiado, vales e consultas simples de movimentação. |
| **Módulo 5 – Operação e controle interno** | Ponto simplificado, manutenção, higiene e ocorrências. |
| **Módulo 6 – Relatórios** | Vendas, estoque, fiado, desperdício, vales e visão gerencial básica. |

---

## 10. Encerramento

Este documento não substitui entrevistas, observação de campo e refinamento técnico posterior. Seu papel é consolidar a transição entre a história do problema e a fase de especificação inicial, criando base consistente para modelagem, implementação e futuras decisões de arquitetura.

