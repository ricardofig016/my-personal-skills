---
name: implement-plan
description: Implement a plan end to end. Use when asked to implement, build, or carry out a plan file.
---

Carry the plan to the end: every step in place, every verification in the plan run, and the plan itself left true, so a reader who opens it after you finish is not misled by anything in it.

Take the plan the user names. When that is ambiguous, ask for the path before doing anything else, then read the plan in full before your first action.

## Mode

The mode decides how the work gets done, so it is settled before the work starts, right after you read the plan.

The user's message may name it. When the message does not, ask as your first act after reading the plan, in a single `ask_user_question` call headed `select mode`, with one option per mode in this order:

1. `simple`. You implement the plan in this session.
2. `auto-audit`. You implement it, then a fresh subagent audits the plan and you close the loop on what it finds.
3. `orchestrator`. You manage the work, and every piece of it goes to a subagent.

Recommend one from the plan's size, how many independent areas it touches, and how breaking it is, and append `(recommended)` to that option.

Then load the mode's files and follow it on top of this one.

| mode | load |
| --- | --- |
| simple | this file alone |
| auto-audit | `auto-audit.md`, `audit.md` |
| orchestrator | `orchestrator.md`, `dispatch.md`, `audit.md` |

## The work

The repository's own rules win wherever they disagree with this file.

Steps go in order, and each step is done when its completion criterion holds. Code that exists is not a finished step. When a criterion is too vague to tell done from not done, that is a question for the user, not a judgement call to make quietly.

Run the tests that cover what you touched. The full suite belongs to the end of the work, where it runs once.

Update the plan in the same task that changed the decision. Correct a claim the plan made about the existing code, since the plan was written from research and research goes stale and misses details. A departure from what the plan intended is a different thing, and it gets reported rather than rewritten away, so the reader can see the choice that was made.

Never assume or guess anything, ask the user. A decision that belongs to the user goes through the `interview` skill, including a question that appears mid-implementation. A blocker gets raised before it gets worked around.
