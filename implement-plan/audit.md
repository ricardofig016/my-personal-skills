# The audit

An audit is an independent read of the plan against the code. It fixes nothing, and it is not the closing decision. Its value is a fresh reader aimed at the places you already believe are weak.

## Dispatching it

One fresh subagent. Tell it to invoke `audit-plan` on the plan, to judge the code against the plan and the repository's rules.

Hand it your suspects: the parts you most doubt, and any earlier finding you want re-verified. A short suspect list aims an audit better than a longer brief does.

## What comes back

A finding is an input, not a verdict. An auditor working from the plan alone misreads what it has not read closely, and it cannot tell a deliberate decision from a mistake. Check each finding before acting on it, and record the ones you drop, with the reason, so the next audit does not raise them again.

Audit again after the fixes, with a fresh auditor, handing it the findings you want re-verified rather than the whole previous report.
