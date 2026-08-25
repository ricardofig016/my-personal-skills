---
name: commit
description: Create well-formed git commits.
disable-model-invocation: true
---

Commit all local changes, structured into logical commits. Run the git commands yourself; never push to remote.

## Behavior

1. Analyze all local changes: `git status` for staged, unstaged, and untracked files. Treat the whole working tree as the change set.
2. Structure the changes into commits. Default to a single `git add -A` plus one commit. Split into multiple commits only when the changes span clearly unrelated concerns (different features, different fixes, unrelated areas). When splitting, stage each group's paths separately with `git add <paths>` and commit each in turn.
3. For each commit, generate a conventional message and run `git commit -m`.

Run the commits immediately. Do not ask for confirmation. Do not push, tag, or touch the remote in any way: local commits only.

## Commit format

```
<type>(<scope>): <description>

[optional body]
```

- All lowercase: type, scope, and description.
- `<description>` is concise, imperative, one line.
- `<scope>` names the area the change touches. Qualify until it is identifiable: a bare name that collides with another area or leaves the location unclear is not enough. Separate levels with `/`
- Add a body only when it adds value, and describe only the changes this commit makes, other details are not needed.

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
