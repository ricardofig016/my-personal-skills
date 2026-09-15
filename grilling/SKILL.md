---
name: grilling
description: Grill the user about a plan, decision, or idea. Use when the user uses any 'grill' trigger phrases or when you want to ask them a question.
---

Interview the user until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

**Calibrate the interrogation to the task.** Being asked to grill is not a mandate for a fixed number of rounds. A complex, lengthy task may need several rounds working the whole tree. A simple task may need one round of two or three questions, or none, if every open point has an obvious answer. How many rounds you run is set by how many questions worth asking are left, not by the ritual.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

**Filter every question before you ask it.** A question earns its place only if the user's answer would actually change what you do. Drop any question that fails this test:

- **Obvious answer.** If one option is clearly better, because the user's request, existing conventions, or common sense already settle it, don't ask. The same goes for options that are effectively equivalent.
- **Validation.** Never ask the user to confirm something you found or verified yourself. Ask only when findings are ambiguous or conflict.
- **Fact-finding.** Facts are yours to dig up, not the user's. A question you could answer by looking at the code, filesystem, or tools is a waste of the user's time. Dispatch a sub-agent instead.

What remains after the filter are the _decisions_ that belong to the user: taste, tradeoffs, intent, constraints only they know.

Ask every round through the `ask_user_question` tool, never as free-form prose. Build one call whose `questions` array holds the whole frontier for this round, one entry per frontier question:

- `id`: a stable slug for the question.
- `question`: the question body, multiple paragraphs if needed, including the choices.
- `header`: a short title for the question.
- `options`: the choices, each with a `label` and a one-sentence `description`. Put your recommended answer first. Omit `options` when the question is genuinely open-ended.
- `multi_select`: `true` only when the answer can legitimately include more than one option.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier, reapply the filter, and ask the next round with another `ask_user_question` call. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Screen every round for hidden dependencies before you send it. A question whose validity, options, or relevance changes based on how another question in the same round is answered does not belong in this round. Defer it to the next round; ask it only once its prerequisite is settled.

When a frontier question needs a fact from the environment, dispatch a sub-agent to find it and don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. Some things can only be found by the user; accept that and ask them to look.

The session is done when the filtered frontier is empty: every decision that would change the outcome is settled, and nothing worth asking remains. Wrap up in a sentence or two covering what was decided and what you assumed, then start acting. Do not ask the user to confirm the confirmation.
