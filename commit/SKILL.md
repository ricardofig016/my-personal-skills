---
name: commit
description: Create well-formed git commits whose messages accurately summarize every committed change.
disable-model-invocation: true
---

Commit all local changes, structured into logical commits. Run the git commands yourself; never push to remote.

## Behavior

1. **Inspect the entire change set before choosing a message.** Run `git status --short`, then inspect both staged and unstaged content with `git diff`, `git diff --cached`, and a summary such as `git diff HEAD --stat`. Treat tracked, staged, unstaged, deleted, renamed, and untracked files as part of the change set. Do not infer the scope.
2. **Read the actual patches.** For every changed path, identify what behavior, presentation, configuration, generated artifact, or documentation changed. If a generated file is present, trace it back to the source change and include that fact in the scope analysis. Use `git diff HEAD -- <paths>` or equivalent so staged changes are not missed.
3. **Structure the changes into commits.** Default to a single `git add -A` plus one commit. Split into multiple commits when the changes span clearly unrelated concerns, such as separate features, fixes, or areas. When splitting, stage each group's paths separately with `git add <paths>` and commit each in turn.
4. **Use a body when it prevents an incomplete summary.** The body should mention the major related changes that are not obvious from the subject. Keep it factual and limited to what this commit changes. Do not claim validation, behavior, or files that the patch does not support.

Run the commits immediately. Do not ask for confirmation. Do not push, tag, reset, rebase, amend, or touch the remote unless the user explicitly asks for that operation.

## Commit format

```
<type>(<scope>): <description>

[optional body]
```

- All lowercase: type, scope, and description.
- `<description>` is concise, imperative, one line.
- `<scope>` names the area the change touches. Qualify until it is identifiable: a bare name that collides with another area or leaves the location unclear is not enough. Separate levels with `/`.
- Add a body when it helps summarize multiple related aspects of the inspected patch.
- The subject and body must reflect the actual complete patch, including generated artifacts.

## Types

- feat: new feature
- fix: bug fix
- docs: documentation changes
- style: code style changes
- refactor: code refactoring
- test: adding or modifying tests
- chore: maintenance tasks
- {custom}: when the other types do not fit

## Example

```
feat(server/auth): add password reset flow

- add forgot password form
- implement email verification
- add password reset endpoint
```
