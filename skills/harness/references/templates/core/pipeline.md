# Pipeline Template

## Pattern

Sequential work where each step consumes the previous step's output.

```text
[Analysis] -> [Design] -> [Production] -> [Verification]
```

## Use When

- Steps have strong dependencies.
- Each role needs a clearly defined input from the prior role.
- Auditability matters more than parallel speed.

## Role Layout

| Step | Role | Responsibility | Input | Output |
|---|---|---|---|---|
| 1 | analyst | Understand goals and constraints | User brief | `_workspace/01_analyst_requirements.md` |
| 2 | designer | Produce the plan/spec | analyst output | `_workspace/02_designer_plan.md` |
| 3 | producer | Create the artifact | designer output | `_workspace/03_producer_artifact.md` |
| 4 | verifier | Check quality and gaps | producer output | `_workspace/04_verifier_report.md` |

## Orchestration

1. Check existing `_workspace/` state.
2. Create or reuse session/context files.
3. Run each role in order.
4. Stop on blocking failure, or continue with explicit missing-result notes when safe.
5. Summarize final result and update handoff files.

## Failure Policy

| Situation | Strategy |
|---|---|
| Step fails | Retry once, then report the failed step and stop or continue with a gap note |
| Prior output is unclear | Ask for clarification or send back to prior step |
| Late-stage verification fails | Return to the producing step with specific fixes |
