# Hierarchical Delegation Template

## Pattern

A lead decomposes work into manager-level domains, and managers coordinate lower-level work.

```text
[Lead] -> [Manager A] -> [Worker A1]
       -> [Manager B] -> [Worker B1]
```

## Use When

- The problem naturally decomposes into domains.
- Each domain has meaningful internal subwork.
- A flat team would be too noisy, but deep nesting would lose context.

## Role Layout

| Level | Role | Responsibility |
|---|---|---|
| L0 | lead | Overall decomposition and final decisions |
| L1 | domain lead | Own one domain and coordinate subwork |
| L2 | worker | Complete a bounded task |

## Orchestration

1. Keep depth to two levels by default.
2. Prefer flattening when runtime does not support nested delegation.
3. Require each manager to write a domain summary.
4. Lead integrates domain summaries into final output.

## Failure Policy

| Situation | Strategy |
|---|---|
| L2 worker fails | Domain lead retries once or reduces scope |
| L1 lead fails | Overall lead takes over or asks user |
| Context loss appears | Flatten the structure and use file-based handoff |
