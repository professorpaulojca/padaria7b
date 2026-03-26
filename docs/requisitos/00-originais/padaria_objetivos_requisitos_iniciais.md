# padaria_objetivos_requisitos_iniciais

> Fonte: `docs/padaria_objetivos_requisitos_iniciais.docx`

Padaria – Objetivo do Sistema e Requisitos Iniciais
Documento complementar à história do problema

1. Premissas adotadas
- O sistema será pensado para uma padaria pequena ou média, com operação completa, porém ainda sem organização formal de processos.
- O uso principal será por pessoas com pouca familiaridade com computador, planilhas e sistemas de gestão.
- O projeto acadêmico poderá ser implementado com interface em React, backend em Java e persistência em PostgreSQL, conforme a orientação do curso.
- Os requisitos abaixo representam um ponto de partida consistente; eles ainda podem ser refinados após entrevistas e observação da rotina real da padaria.

2. Objetivo geral do sistema
Desenvolver um sistema de controle geral para padarias de pequeno porte, capaz de apoiar a administração do negócio, reduzir perdas operacionais e organizar informações que hoje ficam dispersas em cadernos, na memória do proprietário e em rotinas informais do dia a dia.
3. Objetivos específicos
- Controlar estoque de ingredientes, produtos prontos, materiais de apoio e itens de limpeza.
- Registrar vendas de balcão, mesas, entrega própria e plataformas de delivery.
- Controlar clientes que compram fiado e facilitar cobrança, baixa de pagamento e histórico.
- Acompanhar jornada, vales e ocorrências ligadas aos funcionários.
- Apoiar manutenção e higiene do estabelecimento por meio de registros simples e alertas básicos.
- Dar ao dono uma visão mais clara do que entra, do que sai, do que falta, do que sobra e do que está gerando prejuízo.

4. Escopo inicial do sistema

5. Requisitos funcionais iniciais

6. Requisitos não funcionais iniciais

7. Requisitos operacionais e de uso
- Deve existir pelo menos um ponto principal de atendimento para lançamento de pedidos, com possibilidade de expansão para caixa, cozinha e administração.
- O fluxo do atendente deve exigir o menor número possível de cliques para abrir, editar e finalizar um pedido.
- Os pedidos destinados à cozinha ou chapa devem ficar visíveis de forma clara, com identificação de prioridade e situação atual.
- Comandas, quando adotadas, devem possuir forma simples de abertura e fechamento, evitando perda de controle entre consumo e pagamento.
- O registro de fiado deve ser formalizado dentro do sistema para substituir ou complementar o caderno manual.
- As rotinas de higiene devem ser registradas em formato simples, como checklist ou ocorrência, para apoiar disciplina operacional.
- As operações críticas da padaria devem continuar possíveis mesmo com usuários de pouca alfabetização digital.
- A solução deve prever uso predominantemente via navegador, em rede local ou internet, conforme disponibilidade da implantação real.

8. Perguntas de levantamento que ainda precisam ser respondidas
- Quantos computadores ou dispositivos realmente existirão na padaria?
- Haverá apenas um ponto de atendimento ou mais de um ao mesmo tempo?
- O pedido será feito no balcão, no caixa, na mesa ou em mais de um local?
- A cozinha/chapa terá monitor, impressora ou apenas acompanhamento por voz?
- As comandas serão físicas, digitais ou ambas?
- O fiado continuará existindo para qualquer cliente ou apenas para clientes cadastrados?
- O dono quer controlar receita por produto ou apenas vendas totais?
- Será necessário saber o custo aproximado das vendas por iFood?
- Quem será responsável por registrar limpeza, manutenção e troca de filtro?
- Os funcionários aceitarão registrar ponto e vales em sistema ou haverá resistência inicial?

9. Sugestão de organização modular para implementação acadêmica

10. Encerramento
Este documento não substitui entrevistas, observação de campo e refinamento técnico posterior. Seu papel é consolidar, com rigor acadêmico, a transição entre a história do problema e a fase de especificação inicial do sistema, criando uma base consistente para modelagem, implementação e futuras decisões de arquitetura.

### Tabela 1
| Finalidade deste documento: registrar, após a etapa da história, o objetivo geral do sistema e um primeiro conjunto de requisitos funcionais, não funcionais, operacionais e de dados para um sistema de controle geral de padaria. |
| --- |

### Tabela 2
| Faixa de escopo | Descrição |
| --- | --- |
| Incluído no escopo inicial | Estoque, vendas, fiado, caixa, vales, cadastro de funcionários, cadastro de clientes, pedidos internos, pedidos de mesa, controle básico de manutenção e higiene, relatórios gerenciais simples. |
| Incluído de forma simplificada | Integração conceitual com iFood/delivery, controle de ponto e controle de produção/cozinha, todos inicialmente em nível acadêmico e adaptável. |
| Fora do escopo inicial | Contabilidade formal, emissão fiscal completa, integração bancária, folha de pagamento completa, BI avançado, automações industriais e integrações externas complexas. |

### Tabela 3
| Código | Descrição do requisito funcional |
| --- | --- |
| RF01 | O sistema deve permitir cadastrar produtos vendidos pela padaria, com descrição, categoria, preço, unidade de venda e situação de disponibilidade. |
| RF02 | O sistema deve permitir cadastrar ingredientes e insumos da produção, como trigo, sal, fermento, leite, manteiga, açúcar e café. |
| RF03 | O sistema deve registrar entrada e saída de estoque, identificando motivo, quantidade, data e responsável pelo lançamento. |
| RF04 | O sistema deve permitir consultar itens em falta, itens abaixo do mínimo e itens com sobra excessiva. |
| RF05 | O sistema deve registrar perdas, desperdícios, vencimentos e descarte de mercadorias. |
| RF06 | O sistema deve permitir cadastrar fornecedores e registrar compras de mercadorias e materiais. |
| RF07 | O sistema deve registrar vendas de balcão e consolidar os itens vendidos em cada atendimento. |
| RF08 | O sistema deve permitir registrar pedidos para consumo em mesas, identificando mesa, itens e situação do pedido. |
| RF09 | O sistema deve permitir registrar pedidos para viagem e entrega, inclusive separando pedidos presenciais e pedidos de plataformas. |
| RF10 | O sistema deve permitir controlar comandas ou pedidos abertos até sua finalização. |
| RF11 | O sistema deve permitir registrar pagamentos por dinheiro, cartão, pix e fiado. |
| RF12 | O sistema deve manter cadastro de clientes que compram fiado e registrar histórico de débitos, pagamentos e saldo pendente. |
| RF13 | O sistema deve permitir registrar retirada de vale para funcionário, vinculando data, valor, observação e posterior desconto. |
| RF14 | O sistema deve manter cadastro de funcionários com função, situação e dados básicos para controle operacional. |
| RF15 | O sistema deve permitir registrar horários de entrada e saída dos funcionários, inclusive correções justificadas. |
| RF16 | O sistema deve permitir registrar tarefas ou ocorrências de manutenção, como aferição de balanças, afiação de corta-frios e troca de filtro. |
| RF17 | O sistema deve permitir registrar rotinas de higiene e limpeza, como limpeza de mesas, salão, banheiro, cozinha e descarte de lixo. |
| RF18 | O sistema deve permitir identificar pedidos pendentes de preparo para cozinha/chapa, com status como aguardando, em preparo e concluído. |
| RF19 | O sistema deve gerar relatórios simples de vendas, estoque, fiado, funcionários, vales, desperdício e custos operacionais. |
| RF20 | O sistema deve manter histórico de movimentações para consulta posterior pelo proprietário ou gerente. |

### Tabela 4
| Código | Descrição do requisito não funcional |
| --- | --- |
| RNF01 | A interface deve ser simples, com poucos passos por operação, linguagem clara e uso de botões de fácil identificação. |
| RNF02 | O sistema deve ser utilizável por pessoas com pouca familiaridade com computador, priorizando legibilidade e navegação objetiva. |
| RNF03 | O sistema deve possuir autenticação por usuário e senha, com perfis de acesso compatíveis com cada função. |
| RNF04 | As operações relevantes devem ficar registradas para auditoria básica, como data, hora e usuário responsável. |
| RNF05 | O sistema deve preservar dados mesmo em encerramento normal da aplicação, por meio de persistência em banco PostgreSQL. |
| RNF06 | Deve ser possível executar rotina de backup periódico do banco de dados. |
| RNF07 | O tempo de resposta das operações comuns deve ser compatível com uso em balcão, evitando lentidão excessiva no atendimento. |
| RNF08 | A solução deve permitir evolução futura para múltiplos pontos de atendimento sem exigir reescrita completa do sistema. |
| RNF09 | O sistema deve apresentar mensagens de erro compreensíveis ao usuário e não apenas mensagens técnicas. |
| RNF10 | O sistema deve ser organizado em camadas coerentes, alinhando interface, lógica de negócio e persistência de dados. |
| RNF11 | Os nomes de telas, campos e relatórios devem refletir a linguagem cotidiana da padaria. |
| RNF12 | A aplicação deve possibilitar manutenção acadêmica e futura expansão sem acoplamento excessivo entre módulos. |

### Tabela 5
| Módulo | Conteúdo principal |
| --- | --- |
| Módulo 1 – Cadastros | Clientes, funcionários, fornecedores, produtos, ingredientes, categorias e mesas. |
| Módulo 2 – Estoque e compras | Entradas, saídas, perdas, níveis mínimos e compras de insumos e materiais. |
| Módulo 3 – Atendimento e pedidos | Balcão, mesas, comandas, viagem, delivery e status de preparo. |
| Módulo 4 – Caixa e financeiro básico | Pagamentos, fiado, vales e consultas simples de movimentação. |
| Módulo 5 – Operação e controle interno | Ponto simplificado, manutenção, higiene e ocorrências. |
| Módulo 6 – Relatórios | Vendas, estoque, fiado, desperdício, vales e visão gerencial básica. |