# 🧭 copilot Memory System (Brownfield / Projeto em Andamento)

Este workspace utiliza o **copilot** com um **Memory Bank** para maximizar **acurácia** em projetos já existentes (front + backend + Oracle/PLSQL),
com documentação viva e mudanças seguras.

## 📁 Estrutura

```
.copilot/
├── core/          → Identidade, regras e padrões obrigatórios (alta acurácia)
├── memory/        → Memory Bank (continuidade entre sessões)
├── workflows/     → Workflows (Plan/Act/Retry/Learning/Task Logs)
└── diagrams/      → Diagramas mermaid (opcional)
```

## ✅ Como trabalhar (fluxo recomendado)

1. **Coloque documentos do cliente em** `/docs/requisitos/00-originais`
2. **Normalização**: gere `/docs/requisitos/01-normalizados` (MD limpo)
3. **Consolidação**: gere `/docs/requisitos/02-mapa/mapa-mestre.md`
4. **Spec/Plan/Tasks**: crie specs por **fluxo crítico** e implemente com mudanças pequenas

## 🧠 Memory Bank é a fonte de verdade
- O copilot não mantém memória interna entre sessões.
- `activeContext.md` e `progress.md` devem refletir o estado real do projeto.

## ⚙️ Stack alvo
Angular + C# (.NET Core 3.1) + Oracle (PL/SQL)

> Nota: .NET Core 3.1 é legado; registre riscos e plano incremental em `riskRegister.md` quando aplicável.

