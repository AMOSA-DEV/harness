# Fan-out/Fan-in Template

## Pattern

Multiple specialists inspect the same input from different angles, then an integrator merges results.

```text
          -> [Specialist A] -
[Input]  -> [Specialist B]  -> [Integrator]
          -> [Specialist C] -
```

## Use When

- Independent perspectives improve quality.
- Parallel research/review is useful.
- The final answer must reconcile conflicting findings.

## Role Layout

| Role | Responsibility | Output |
|---|---|---|
| specialist-a | Perspective or domain A | `_workspace/02_specialist_a.md` |
| specialist-b | Perspective or domain B | `_workspace/02_specialist_b.md` |
| specialist-c | Perspective or domain C | `_workspace/02_specialist_c.md` |
| integrator | Merge, reconcile, and prioritize | `_workspace/03_integrated_report.md` |

## Orchestration

1. Define independent work packets.
2. Run specialists in parallel when the runtime allows it; otherwise run sequentially with isolated prompts.
3. Require each specialist to write a file output.
4. Integrator reads all outputs, preserves disagreements, and cites source role names.
5. Produce final report.

## Failure Policy

| Situation | Strategy |
|---|---|
| One specialist fails | Continue with remaining results and mark the missing perspective |
| Majority fail | Pause and ask whether to retry or narrow scope |
| Results conflict | Preserve both claims with evidence and confidence |
