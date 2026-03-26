# Workflow: Plan Mode

Objetivo: entender o problema, o contexto e produzir um plano sólido antes de escrever código.

```mermaid
flowchart TD
    Start[Start: initializeProject] --> CheckExists{checkMemoryBankExists}

    CheckExists -->|No| CreateDir[createMemoryBankDirectory]
    CreateDir --> ScaffoldMB[scaffoldMemoryBankStructure]
    ScaffoldMB --> PopulateFiles[populateMemoryBankFiles]
    PopulateFiles --> ReadFiles[readMemoryBank]

    CheckExists -->|Yes| ReadFiles

    ReadFiles --> CheckFiles{verifyFilesComplete}

    CheckFiles -->|No| CreateMissing[createMissingFiles]
    CreateMissing --> Plan[createPlan]

    CheckFiles -->|Yes| Verify[verifyContext]

    Plan --> Document[documentPlanning]
    Verify --> Strategy[developStrategy]
    Strategy --> Present[presentApproach]
```

Etapas principais:
- Inicializar projeto, verificar/ criar Memory Bank.
- Ler contexto de todos os arquivos relevantes.
- Analisar problema (`analyzeProblem`).
- Criar plano (`createPlan`).
- Documentar plano em `.windsurf/plans/`.
- Desenvolver estratégia e apresentá-la ao usuário.
