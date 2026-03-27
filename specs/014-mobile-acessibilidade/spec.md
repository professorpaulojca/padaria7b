# SPEC-014 — Mobile e Acessibilidade

> **Módulo**: M14 – Mobile e Acessibilidade  
> **Status**: Rascunho  
> **Data**: 2026-03-26  
> **Prioridade**: CRÍTICA — Define arquitetura de UX desde o início  
> **Fontes**:
> - `docs/requisitos/01-normalizados/padaria_historia_operabilidade.md`
> - `docs/requisitos/01-normalizados/padaria_objetivos_requisitos_iniciais.md`
> - `docs/requisitos/03-planejamento-expandido/planejamento-modular-v2.md`

---

## 1. Objetivo do Módulo

Este módulo é **transversal** — não é uma funcionalidade isolada, mas um conjunto de diretrizes e requisitos que definem como TODOS os outros módulos devem ser construídos: responsivo, instalável como app (PWA), acessível a pessoas com pouca experiência digital, e com capacidade offline básica.

### Evidências

- **INF-03**: "Web (rede local + internet)."
- **INF-04**: "Caixa, pedidos internos e consulta básica devem funcionar offline."
- **INF-05**: "Interface simples, poucos campos, botões claros, fluxo fácil."
- **PES-01**: "Sistema objetivo, poucas opções por vez."
- **PES-02**: "Letras maiores e poucos botões. Diretriz forte."
- **RNF01**: "Interface simples, poucos passos por operação, linguagem clara."
- **RNF02**: "Utilizável por pessoas com pouca familiaridade com computador."
- **RNF11**: "Nomes de telas, campos e relatórios refletindo linguagem cotidiana da padaria."

---

## 2. Requisitos Funcionais e Não Funcionais

| Código | Descrição | Origem |
|---|---|---|
| **RF-MOB-01** | **PWA**: sistema instalável no celular/tablet como app, sem loja de aplicativos. Manifest + service worker. | INF-03, ROP-08 |
| **RF-MOB-02** | **Design responsivo**: todas as telas adaptam-se a desktop (1024+), tablet (768-1024) e smartphone (< 768) | PES-02 |
| **RF-MOB-03** | **Modo offline básico**: caixa, consulta de preços e registro de pedidos funcionam sem internet; sincroniza ao reconectar | INF-04 |
| **RF-MOB-04** | Fonte grande (mínimo 16px body, 20px+ botões), botões amplos (mínimo 44x44px touch target), espaçamento generoso | PES-02, RNF01 |
| **RF-MOB-05** | Tela inicial personalizada por perfil: Atendente → Pedidos, Caixa → Caixa, Chapeiro → Fila de Preparo, Dono → Dashboard | INF-06 |
| **RF-MOB-06** | Notificações push para alertas críticos: estoque mínimo, validade, manutenção, fiado em atraso | Proatividade |
| **RF-MOB-07** | Leitura de código de barras via câmera do dispositivo (para busca de produto e conferência de estoque) | Agilidade |
| **RF-MOB-08** | Feedback visual e sonoro: confirmação de ações, alertas, erros | RNF09, PES-01 |
| **RF-MOB-09** | **Linguagem cotidiana** da padaria em todos os rótulos, mensagens, ícones e instruções | RNF11 |
| **RF-MOB-10** | Acessibilidade básica: contraste adequado (WCAG AA), textos legíveis, sem dependência exclusiva de cor | PES-01 |

---

## 3. Diretrizes de Design (Design System)

### 3.1 Princípios Fundamentais

| # | Princípio | Aplicação |
|---|---|---|
| 1 | **Simplicidade acima de tudo** | Máximo 3-5 ações por tela. Sem menus profundos. |
| 2 | **Linguagem da padaria** | "Pedido", "Comanda", "Fiado", "Vale", "Caixa" — nunca termos técnicos. |
| 3 | **Toque primeiro, mouse segundo** | Projetado para tela sensível ao toque. Mouse é suportado mas não prioritário. |
| 4 | **Feedback imediato** | Toda ação dá retorno visual/sonoro em < 200ms. |
| 5 | **Perdão de erros** | Confirmação antes de ações destrutivas. Desfazer quando possível. |

### 3.2 Tipografia

| Elemento | Tamanho Mínimo | Peso |
|---|---|---|
| Corpo de texto | 16px | Regular |
| Labels de campo | 14px | Medium |
| Títulos de seção | 20px | Bold |
| Títulos de tela | 24px | Bold |
| Botões | 16px | Bold |
| Valores monetários | 20px | Bold |

### 3.3 Cores e Contraste

| Uso | Cor sugerida | Contraste |
|---|---|---|
| Primária (ações) | Laranja/Âmbar quente (tema padaria) | ≥ 4.5:1 sobre branco |
| Sucesso | Verde | ≥ 4.5:1 |
| Alerta | Amarelo/Âmbar | ≥ 4.5:1 |
| Erro/Perigo | Vermelho | ≥ 4.5:1 |
| Fundo | Branco / Cinza claro | — |
| Texto | Cinza escuro (#333) | ≥ 7:1 sobre branco |

### 3.4 Componentes de Toque

| Componente | Tamanho Mínimo | Espaçamento |
|---|---|---|
| Botão primário | 48px altura, 120px largura | 8px entre botões |
| Botão de ação flutuante (FAB) | 56x56px | — |
| Item de lista clicável | 48px altura | 4px entre itens |
| Checkbox / Radio | 24x24px (área de toque 44x44px) | 12px entre opções |
| Campo de input | 48px altura | 12px entre campos |

### 3.5 Navegação

| Dispositivo | Padrão de Navegação |
|---|---|
| Desktop | Menu lateral fixo (sidebar) + header com usuário/logout |
| Tablet | Menu lateral colapsável (hamburger) + header |
| Smartphone | Bottom navigation bar (4-5 ícones) + header compacто |

---

## 4. PWA — Requisitos Técnicos

### 4.1 Manifest

| Campo | Valor |
|---|---|
| `name` | "Padaria — Sistema de Gestão" |
| `short_name` | "Padaria" |
| `display` | `standalone` |
| `orientation` | `any` |
| `theme_color` | Cor primária do tema |
| `background_color` | Branco |
| `icons` | 192x192, 512x512 (PNG) |

### 4.2 Service Worker — Estratégia de Cache

| Recurso | Estratégia | Justificativa |
|---|---|---|
| App shell (HTML, CSS, JS) | Cache First | Carregamento rápido e offline |
| Dados de produto/preço | Stale While Revalidate | Acessível offline, atualizado quando online |
| Imagens de produto | Cache First | Não mudam frequentemente |
| Dados de pedido/venda | Network First + Offline Queue | Precisam ser sincronizados |
| Relatórios | Network Only | Sempre dados frescos |

### 4.3 Funcionalidades Offline

| Funcionalidade | Disponível Offline? | Comportamento |
|---|---|---|
| Login | Sim (sessão cacheada) | Valida PIN localmente |
| Criar pedido | Sim (com fila) | Enfileira e sincroniza |
| Consultar preço/produto | Sim | Dados do último sync |
| Registrar pagamento | Sim (com fila) | Enfileira e sincroniza |
| Fila de preparo | Parcial | Exibe estado cacheado, sem atualização real-time |
| Estoque / Relatórios | Não | Mensagem "Sem conexão" |
| Cadastros | Não | Mensagem "Sem conexão" |

### 4.4 Sincronização

| Aspecto | Estratégia |
|---|---|
| Detecção | `navigator.onLine` + ping ao servidor |
| Fila offline | IndexedDB local com timestamp e ordem |
| Sync | Ao reconectar, processa fila em ordem (FIFO), resolvi conflitos por timestamp |
| Conflito | Último write ganha (last-write-wins) com log de conflito |
| Indicador visual | Banner "Modo Offline" visível em todas as telas |

---

## 5. Leitura de Código de Barras

| Aspecto | Especificação |
|---|---|
| Tecnologia | API `navigator.mediaDevices.getUserMedia` + biblioteca JS de decodificação (ex.: QuaggaJS, ZXing) |
| Formatos | EAN-13, EAN-8, UPC-A |
| Uso em M01 | Cadastrar produto com código de barras via câmera |
| Uso em M03 | Conferência de estoque: escanear para buscar item |
| Uso em M06 | Buscar produto rapidamente ao montar pedido |
| Fallback | Se câmera não disponível, digitar código manualmente |

---

## 6. Notificações Push

| Tipo de Alerta | Módulo | Prioridade |
|---|---|---|
| Estoque abaixo do mínimo | M03 | Alta |
| Produto próximo do vencimento | M03 | Alta |
| Manutenção preventiva pendente | M11 | Média |
| Fiado em atraso (> X dias) | M08 | Média |
| Conta a pagar próxima do vencimento | M08 | Média |
| Checklist de limpeza não realizado | M10 | Baixa |
| Dedetização próxima do vencimento | M10 | Média |
| Pedido pronto (para atendente) | M06 | Alta |

### Regras

- Notificações push requerem consentimento do usuário (padrão web).
- Apenas Dono e Gerente recebem todas as notificações. Demais perfis recebem apenas as relevantes.
- Frequência de notificação é configurável em M13 (para evitar excesso).

---

## 7. Regras de Negócio

| ID | Regra |
|---|---|
| **RN-MOB-01** | Toda tela deve ser funcional em viewport de 320px de largura mínima (smartphone pequeno). |
| **RN-MOB-02** | Nenhuma funcionalidade pode depender exclusivamente de hover (mouse). Sempre ter alternativa de toque. |
| **RN-MOB-03** | Dados offline são armazenados em IndexedDB (não localStorage) para suportar volumes maiores. |
| **RN-MOB-04** | Fila offline tem limite máximo de 500 operações. Acima disso, bloqueia novas operações com aviso. |
| **RN-MOB-05** | Ao reconectar, sincronização é automática e exibe progresso ("Sincronizando 15 de 23 operações..."). |
| **RN-MOB-06** | Conflitos de sincronização são registrados em log acessível ao Dono para revisão. |
| **RN-MOB-07** | Tempo de carregamento inicial (first paint) deve ser < 3 segundos em conexão 3G. |
| **RN-MOB-08** | Toda mensagem de erro deve ser em linguagem da padaria (RNF09, RNF11). Nunca exibir stack trace ou código HTTP. |

---

## 8. Requisitos de Teste de Acessibilidade

| Teste | Critério |
|---|---|
| Contraste | WCAG AA (4.5:1 texto normal, 3:1 texto grande) |
| Touch targets | Mínimo 44x44px |
| Legibilidade | Testado com zoom de 200% sem quebra de layout |
| Orientação | Funciona em retrato e paisagem |
| Leitor de tela | Labels em todos os inputs, alt em imagens, aria-labels em ícones |
| Teclado | Navegação por tab funcional (para desktop) |
