---
name: audit-plan
description: Audit whether a plan was completed successfully. Use when asked for an implementation audit of a specific plan or plan phase.
---

Audit the plan file or the named plan phase. Treat the audit target as the scope boundary, and hold the implementation to what the target actually meant to achieve rather than to the markers of completion it left behind. End with one result line and the findings that produced it.

## Method

1. Read the whole plan. Recover what the target meant to achieve: its outcomes, constraints, and acceptance criteria. A phase audit takes that phase as its target and reads earlier phases as context, not scope. The boundary runs both ways: work the target deferred is not a finding, and work it claimed is a finding wherever the code disagrees.

2. Judge the code against the plan and the project's rules. A file's name, a plan sentence, and a passing test are all claims of completion, not proof.

3. Classify every mismatch as a real problem, a deliberate change that still serves the target's intent, or unresolved uncertainty. Report deliberate changes and uncertainty under `Related findings`. Naming and wording differences are not findings.

## Report

Keep each finding to one compact entry. Open with exactly one result line:

`Audit result: PASS` or `Audit result: FAIL`

Decide the result from material outcome, not from the number of findings. `PASS` means the target's substantive outcomes exist, are integrated across the correct boundaries, and obey the rules that matter to the design. `FAIL` means a material outcome is missing, wrong, broken, or in breach of a rule the design depends on. Minor problems can stay under `PASS`, and they still belong in the report.

Then these sections, in this order:

### Problems

Real failures against the target or a verified rule. Order by criticality, most severe first. Use this shape:

`<criticality>: <scope> <short title>`

State the problem, where it lives, and what resolves it. No code snippets. A problem can sit here under a `PASS` result when it is real but immaterial to the substantive outcome.

### Related findings

What the target's work has to answer for without being a failure: coherent deviations, decisions that changed the design, stale or self-contradicting documentation, unresolved questions, and evidence that limits confidence. Say whether it preserves the target's intent and whether anyone should follow up. Add a criticality label when urgency warrants one, but never label a deliberate change a failure.

### Incidental findings

What the audit turned up outside the plan: unrelated bugs, and anything else that deserves a mention.

Each entry holds only what a reader needs to understand and locate it. Drop the retelling of how you found it, the restatement of what the plan said, and the evidence.

Omit an empty section. Leave out praise, a tour of the codebase, test output as proof, and any restatement of completed plan items. End after the actionable findings.

## Done

The audit is complete when every material item in the target carries a verified conclusion, every implementation mismatch is classified, every problem has a concrete proposed fix, and the result line follows from those conclusions.
