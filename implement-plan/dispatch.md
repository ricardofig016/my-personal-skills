# Dispatch

## The worker brief

Every dispatch to a new worker is this template, filled in. Later messages to the same worker are plain instructions and need none of it.

Give a worker the steps it owns and nothing more. A worker handed the whole plan will act on parts of it, and parts of it are somebody else's area.

This is a skeleton. Modify it to fit your usecase, the type of work, the requirements of the task, the plan, and the repository.

---

**Task.** The plan path, the steps this worker owns quoted from it, and what done looks like. Quote the steps rather than summarizing them, because the plan's own words carry the criterion the work gets judged against.

**Rules.** Every dispatch carries these.

- Report a file that looks like another worker is changing it, and leave that file alone until you are answered.
- Run only the tests that cover your change.
- When a decision changes, update the plan and say so. Report a departure from what the plan intended instead of rewriting it.
- Keep the tree clean. No scratch files, no stray output, no commits.
- Ask before you work around a blocker, and ask rather than guess.
- The repository's own rules win where they disagree with anything here.

**Report.** Give it in this shape and keep it to a few lines.

- What now works, or why it does not.
- The area it landed in.
- The evidence: the command and its result, and which real thing you checked against.
- What you need from me.
- Anything you decided that the plan did not settle.

High level, short, no code, and no file lists. I care about the outcome, not implementation details.

---
