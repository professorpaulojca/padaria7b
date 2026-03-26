# História e Operabilidade da Padaria

> **Fonte**: `docs/padaria_historia_operabilidade.docx`  
> **Status**: Normalizado — Fase 1 concluída  
> **Tipo**: Levantamento inicial (história do problema + perguntas de operabilidade)

---

## Metadados do Documento

| Campo | Valor |
|---|---|
| Contexto | Comércio padaria de pequeno porte, completo, com operação crescente e controles ainda manuais. |
| Finalidade | Registrar a história do problema e apoiar as etapas seguintes sem pular diretamente para implementação. |
| Base de elaboração | Informações fornecidas pelo usuário sobre a realidade da padaria, ampliadas com situações comuns de operação em padarias pequenas. |

---

## 1. História Narrativa do Problema

> Observação: O texto foi mantido em linguagem simples e narrada, porque esta etapa pede a compreensão da história do negócio antes da definição de requisitos, banco de dados, linguagem ou arquitetura.

### 1.1 Contexto Geral

O dono é proprietário de uma padaria pequena, mas bastante movimentada. Durante muitos anos, controlava quase tudo no caderno, na conversa com funcionários e na memória. Com o crescimento do comércio, esse modelo deixou de funcionar.

### 1.2 Estoque e Produção

- Sobra sal demais, falta trigo (principal ingrediente para pão).
- Não existe controle organizado do que entra, sai e é consumido.
- Itens afetados: trigo, sal, fermento, açúcar, leite, óleo, café, frios, embalagens.
- Sem acompanhamento, compras viram improviso e o desperdício aumenta.

### 1.3 Manutenção e Equipamentos

- Água da produção é filtrada, mas o filtro já está enferrujado.
- Equipamentos sem rotina de manutenção: balanças, freezers, geladeiras, fornos, cortadores de frios, vitrines.
- Problemas só aparecem quando algo para de funcionar.

### 1.4 Atendimento

- Necessidade de aferir balanças, afiar corta-frios, comprar sacolas e embalagens.
- Itens nem sempre são lembrados na hora certa.
- Falta de embalagem adequada gera improviso e imagem ruim.

### 1.5 Mesas e Consumo Local

- Padaria possui mesas para consumo no local.
- Mais circulação, mais pedidos, mais louça/lixo, mais necessidade de limpeza.
- Sem rotina clara, experiência do cliente piora.

### 1.6 Funcionários e Vales

- Controle de jornada não confiável; funcionários mentem no ponto.
- Pagamento a mais em alguns meses.
- Funcionários pedem vale durante o mês; dinheiro sai direto do caixa sem anotação.
- Resultado: confusão sobre quem pegou, quanto pegou, quanto descontar.

### 1.7 Fiado

- Caderninho com nomes e valores devidos.
- Controle frágil: depende de anotações manuais.
- Riscos: esquecimentos, erros, cobranças indevidas, dúvidas sobre pagamentos.

### 1.8 Delivery (iFood)

- Padaria passou a entregar pelo iFood.
- Custo alto; lucro não corresponde à expectativa.
- Sem controle financeiro organizado, não enxerga o que compensa.

### 1.9 Gestão Geral

- Dono não sabe usar planilhas ou sistemas; tudo feito em caderno e conversa.
- Padaria é pequena mas completa: estoque, produção, atendimento, mesa, limpeza, fiado, funcionários, vale, caixa, manutenção, compras, delivery.
- Tudo existe de forma desorganizada e sem controle suficiente.
- Necessidade do sistema surgiu por necessidade real, não por modernidade.

---

## 2. Quadro Consolidado dos Problemas Observados

| Área | Problema observado | Impacto prático |
|---|---|---|
| Estoque | Falta trigo, sobra sal; insumos comprados sem visão confiável do saldo real. | Risco de parar produção, desperdício, compras mal planejadas. |
| Produção | Sem controle do que foi produzido, consumido ou desperdiçado. | Dificuldade para saber o que vender mais, reduzir ou gera perda. |
| Manutenção | Filtro enferrujado, balanças sem aferição, equipamentos sem rotina. | Queda de qualidade, risco operacional, atendimento prejudicado. |
| Atendimento | Falta embalagem, pedidos improvisados, rotina desorganizada nos picos. | Experiência ruim do cliente, retrabalho da equipe. |
| Mesas e limpeza | Consumo no local gera lixo e exige limpeza sem controle de rotina. | Ambiente desorganizado e menos higiênico. |
| Funcionários | Ponto pouco confiável, horas mal controladas, vales sem anotação. | Pagamentos incorretos, conflitos, perda de controle financeiro. |
| Fiado | Anotações em caderninho, sem visão consolidada. | Cobranças confusas, esquecimentos, perda de receita. |
| Delivery | iFood com custo alto, sem visão clara de lucratividade. | Dor de cabeça operacional, decisão financeira pouco informada. |
| Gestão geral | Informações espalhadas em cadernos, memória e conversas. | Dependência excessiva do dono, pouca visibilidade do negócio. |

---

## 3. Perguntas de Operabilidade com Respostas Sugeridas

> As respostas são sugestões preliminares, não decisões finais.

### 3.1 Infraestrutura e Uso do Sistema

| # | Pergunta | Resposta sugerida |
|---|---|---|
| INF-01 | Teremos pontos fixos de rede? | Sim, pelo menos no caixa/balcão, apoio administrativo e próximo da produção/expedição. Wi-Fi como complemento. |
| INF-02 | Sistema em um computador ou vários? | Mais de um ponto: caixa/atendimento + gestão + cozinha (opcional). |
| INF-03 | Sistema será via internet? | Web (rede local + internet). Operação principal não deve depender exclusivamente de internet externa. |
| INF-04 | O que acontece se a internet cair? | Padaria precisa continuar atendendo. Caixa, pedidos internos e consulta básica devem funcionar offline. |
| INF-05 | Pessoas têm dificuldade com computador? | Sim. Interface simples, poucos campos, botões claros, fluxo fácil. |
| INF-06 | Todos usarão o sistema do mesmo jeito? | Não. Perfis: atendente, caixa, dono/gerente, cozinha/produção. |

### 3.2 Atendimento, Pedidos e Comandas

| # | Pergunta | Resposta sugerida |
|---|---|---|
| ATD-01 | Como o atendente faz o pedido? | Tela simples, escolha rápida de itens, poucos cliques, botões/categorias/atalhos visuais. |
| ATD-02 | Pedidos balcão/mesa/entrega no mesmo lugar? | Mesma base de registro, com identificação clara do tipo de atendimento. |
| ATD-03 | Como o chapeiro sabe que tem coisas para fazer? | Fila de preparo em tela de apoio ou impressão resumida. |
| ATD-04 | Tela na cozinha ou pedidos impressos? | Ambas as possibilidades. Depende da estrutura física e custos. |
| ATD-05 | Teremos comandas? | Sim, para mesas. Numeradas e vinculadas à mesa ou cliente. |
| ATD-06 | Fechamento das comandas? | Comanda concentra tudo consumido; encerrada no caixa ao final. Fluxo simples. |
| ATD-07 | Registro de pedidos iFood? | Modalidade separada de venda para análise de volume, custo e resultado. |
| ATD-08 | Vendas iFood dão lucro? | Sistema deve separar essas vendas e cruzar faturamento com taxas/embalagem/custos. |

### 3.3 Fiado, Caixa e Financeiro Diário

| # | Pergunta | Resposta sugerida |
|---|---|---|
| FIN-01 | Como registrar fiados? | Registro por cliente: nome, contato, data, itens/valor, pagamentos, saldo em aberto. |
| FIN-02 | Quem pode lançar/alterar fiado? | Poucas pessoas: dono, gerente ou caixa de confiança. |
| FIN-03 | Como saber quem pagou/quem deve? | Visão visual: clientes em aberto, valores devidos, pagamentos parciais, atrasos. |
| FIN-04 | Registro de vales para funcionários? | Registrar no momento da retirada: valor, funcionário, data, motivo, usuário que lançou. |
| FIN-05 | Quem pode tirar dinheiro do caixa? | Poucas pessoas: dono ou responsável autorizado. Regra clara. |
| FIN-06 | Fechamento do caixa? | Registrar entradas, saídas, sangrias, vales, formas de pagamento. Comparar apurado × real. |
| FIN-07 | Registrar despesas do dia a dia? | Sim. Gastos frequentes (compra emergencial, embalagem, manutenção, limpeza) afetam resultado. |

### 3.4 Estoque, Produção e Compras

| # | Pergunta | Resposta sugerida |
|---|---|---|
| EST-01 | Controle de trigo, sal e outros? | Controle de insumos principais e itens de apoio. Entrada, saída, saldo, alertas de falta. |
| EST-02 | Só produtos prontos ou ingredientes? | Também ingredientes (trigo, sal, fermento, leite, açúcar etc.). |
| EST-03 | Momento certo de comprar? | Níveis mínimos de estoque para itens importantes. Não depender da memória. |
| EST-04 | Quem registra chegada de fornecedores? | Dono ou funcionário de confiança confirma entrada no recebimento. |
| EST-05 | Desperdício de produção? | Registro simples de perdas, sobras e descarte. Perecíveis precisam desse controle. |
| EST-06 | Controlar validade? | Sim, especialmente itens perecíveis e matérias-primas sensíveis. |

### 3.5 Higiene, Manutenção e Qualidade Operacional

| # | Pergunta | Resposta sugerida |
|---|---|---|
| HIG-01 | Controle de higiene? | Checklists simples por período/área: salão, mesas, banheiro, cozinha, equipamentos. |
| HIG-02 | Responsável por limpar mesas e lixo? | Definir responsável por turno. Sem responsabilidade definida, limpeza fica em segundo plano. |
| HIG-03 | Troca do filtro de água? | Item de manutenção periódica: data, estado, alerta para revisão. |
| HIG-04 | Aferição das balanças? | Manutenção programada: verificação, data, responsável. |
| HIG-05 | Manutenção do corta-frios e equipamentos? | Cadastro de equipamentos + rotina de manutenção preventiva e corretiva. |
| HIG-06 | Aviso sobre itens de limpeza/embalagem acabando? | Sim, para materiais que impactam atendimento: sacolas, papel, produtos de limpeza. |

### 3.6 Pessoas, Treinamento e Usabilidade

| # | Pergunta | Resposta sugerida |
|---|---|---|
| PES-01 | Pessoas com dificuldade de ler/escrever no computador? | Sim. Sistema objetivo, poucas opções por vez, rótulos claros, navegação intuitiva. |
| PES-02 | Letras maiores e poucos botões? | Sim. Diretriz forte para o projeto. |
| PES-03 | Será necessário treinamento? | Certamente. Apresentação, acompanhamento inicial, validação com usuários reais. |
| PES-04 | Dono conseguirá usar sozinho? | Deve consultar informações-chave e registrar operações básicas. Não depender de habilidade técnica avançada. |
| PES-05 | Como evitar complicar a rotina? | Foco nas dores reais, telas não técnicas, fluxos curtos. |
| PES-06 | Que tecnologia? | Documento original cita React, Java e PostgreSQL (orientação do curso). |

---

## 4. Fechamento desta Etapa

- Documento **não** entra em classes, tabelas, APIs, casos de uso formais nem modelo de banco.
- O sistema não será apenas caixa ou estoque — nasce da necessidade de organizar uma operação pequena, porém completa e descontrolada.
- Material serve de base para: objetivo do sistema, requisitos funcionais/não funcionais, atores, fluxos, telas e modelagem de dados.

