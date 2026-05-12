# Expert Pool Template

## Pattern

A router chooses only the specialists needed for the input.

```text
[Router] -> {Expert A | Expert B | Expert C}
```

## Use When

- Requests vary by type.
- Running every specialist would waste context or time.
- A lightweight classification step can choose the right path.

## Role Layout

| Role | Responsibility | Trigger |
|---|---|---|
| router | Classify request and select experts | Every request |
| expert-a | Domain A handling | Matching domain A |
| expert-b | Domain B handling | Matching domain B |
| expert-c | Domain C handling | Matching domain C |

## Orchestration

1. Router writes `_workspace/01_router_plan.md`.
2. Run only selected experts.
3. Collect expert outputs.
4. Integrate when more than one expert ran.
5. If no expert is needed, answer directly and record why.

## Failure Policy

| Situation | Strategy |
|---|---|
| Router is uncertain | Select the smallest reasonable expert set or ask briefly |
| Selected expert fails | Continue with a gap note or retry once |
| Wrong expert selected | Update routing rules in the relevant skill/context |
