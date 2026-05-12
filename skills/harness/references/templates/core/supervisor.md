# Supervisor Template

## Pattern

A supervisor partitions variable work and assigns it to workers.

```text
             -> [Worker A]
[Supervisor] -> [Worker B]
             -> [Worker C]
```

## Use When

- The task list is large or discovered during execution.
- Work units can be batched.
- A central role must track progress and rebalance work.

## Role Layout

| Role | Responsibility |
|---|---|
| supervisor | Build task list, assign batches, integrate results |
| worker-n | Complete assigned batch and report status |

## Orchestration

1. Supervisor inventories files/items/tasks.
2. Supervisor estimates complexity and creates batches.
3. Workers process batches.
4. Supervisor collects outputs and assigns follow-up work.
5. Supervisor performs final integration and verification.

## Failure Policy

| Situation | Strategy |
|---|---|
| Worker fails | Retry once or reassign batch |
| Many workers fail | Split batches smaller and reduce scope |
| Supervisor bottleneck | Increase batch size and worker autonomy |
