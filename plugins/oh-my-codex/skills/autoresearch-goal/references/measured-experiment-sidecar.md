# Measured Experiment Sidecar

Use this sidecar when an autoresearch mission can improve through executable experiments. It adapts the small, empirical loop from `karpathy/autoresearch` to OMX while leaving the enclosing autoresearch skill in charge of lifecycle, validation, and completion.

## Freeze the contract

Before editing, record:

- the immutable evaluator, preparation path, data split, and hard correctness gates;
- the primary metric, direction, and noise tolerance;
- runtime, peak memory, and changed non-test LOC as secondary metrics;
- one mutable experiment surface and a fixed step or wall-clock budget;
- the device, seed, command, and output artifact paths needed to reproduce the run.

Do not optimize the evaluator. Do not change data or split semantics between candidates. Establish the baseline with the same command and budget used for candidates.

## Run one falsifiable experiment

For each cycle:

1. State one hypothesis and its smallest code change.
2. Change only the declared experiment surface. Prefer deletion and existing utilities; add no dependency unless the user explicitly authorized it.
3. Run the fixed evaluator and save a compact log plus at least one inspectable output when the mission concerns generated predictions or artifacts.
4. Measure the declared metrics. Synchronize asynchronous accelerators before timing them.
5. Keep or discard the candidate immediately. Prefer an isolated worktree. In a shared dirty worktree, edit only paths proven clean or capture their byte-for-byte baseline first, then verify exact restoration. Never use repository-wide `reset`, `checkout`, `restore`, `clean`, or stash-based rollback against pre-existing changes.
6. Record the result, then choose the next hypothesis from evidence rather than stacking unmeasured ideas.

Use a mission-local `experiments.tsv` for quantitative rows with these columns:

```text
iteration	candidate	hypothesis	primary	runtime_s	peak_memory_mb	non_test_loc	decision	artifact
```

This table is the sole record for candidate measurements. The enclosing OMX ledger remains the source of truth for workflow verdicts; update it only when the configured professor-critic actually runs at a rubric checkpoint or final completion.

## Decision rule

All hard gates must pass. Then:

- keep a candidate when the primary metric improves beyond the declared tolerance;
- when primary quality is equivalent within tolerance, keep only a meaningful runtime, memory, or LOC simplification that introduces no material secondary regression;
- discard regressions, evaluator errors, mixed candidates that cannot identify a cause, and complexity without measured benefit.

Retain the simplest candidate on a practical tie. A baseline and discarded rows stay in the table so failed ideas remain useful evidence.

## Integrity and autonomy

- Label official synthetic, real-world, simulated, and unavailable inputs accurately. Never present a stand-in as a real sample.
- Keep training inputs free of evaluator-only or oracle-only fields. Treat leakage checks as hard gates.
- Use bounded independent subagents for research, review, or verification, but keep one owner for the mutable experiment surface.
- Continue until the enclosing rubric passes, the user interrupts, or a hard blocker has no safe recovery path. A fixed number of attempts or repeated no-ops is not completion.

## Completion evidence

Finish with the retained candidate, baseline comparison, reproducible command, evaluator output, sample artifacts, tests, runtime, memory, LOC, known limitations, and an independent critic result. If any required claim lacks fresh evidence, continue the loop.
