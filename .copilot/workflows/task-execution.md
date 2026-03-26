# Workflow: Task Execution (alto nível)

```xml
<Workflow id="taskExecution">
  <Step function="loadContext"/>
  <Step function="createPlan"/>
  <Step function="executeTool"/>
  <Step function="checkSuccess"/>
  <Step workflow="retryMechanism" condition="!success"/>
  <Step function="documentCompletion"/>
</Workflow>
```
