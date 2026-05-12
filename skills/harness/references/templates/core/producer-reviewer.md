# Producer-Reviewer Template

## Pattern

A producer creates an artifact and a reviewer checks it against explicit criteria.

```text
[Producer] -> [Reviewer] -> [Producer fixes if needed]
```

## Use When

- Quality criteria can be stated.
- Independent review materially improves output.
- Rework loops are acceptable but must be bounded.

## Role Layout

| Role | Responsibility | Output |
|---|---|---|
| producer | Create or revise the artifact | `_workspace/02_draft.md` |
| reviewer | Judge against criteria and request fixes | `_workspace/03_review.md` |

## Orchestration

1. Define review criteria before generation.
2. Producer writes draft.
3. Reviewer returns `PASS`, `FIX`, or `REDO`.
4. Producer revises at most two times unless user asks otherwise.
5. Final output includes remaining known issues.

## Failure Policy

| Situation | Strategy |
|---|---|
| Reviewer finds fixable issues | Send specific fix list to producer |
| Reviewer requests full redo twice | Stop and report unresolved quality issues |
| Criteria are ambiguous | Ask user or create a temporary rubric |
