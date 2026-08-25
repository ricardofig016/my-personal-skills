---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Ask every round through the `ask_user_question` tool, never as free-form prose. Build one call whose `questions` array holds the whole frontier for this round, one entry per frontier question:

- `id`: a stable slug for the question.
- `question`: the question body, multiple paragraphs if needed, including the choices.
- `header`: a short title for the question.
- `options`: the choices, each with a `label` and a one-sentence `description`. Put your recommended answer first. Omit `options` when the question is genuinely open-ended.
- `multi_select`: `true` only when the answer can legitimately include more than one option.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round with another `ask_user_question` call. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Screen every round for hidden dependencies before you send it. A question whose validity, options, or relevance changes based on how another question in the same round is answered does not belong in this round. Defer it to the next round; ask it only once its prerequisite is settled.

Finding _facts_ is mostly your job, rarely the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait. Some things can only be found by the user, accept that and simply ask the user to find it.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
