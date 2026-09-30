---
name: commit
description: Create well-formed git commits whose messages accurately summarize every committed change.
disable-model-invocation: true
---

Commit all local changes, structured into logical commits, running every git command yourself.

## Behavior

1. **Find the repository that owns the changes.** Run `git rev-parse --show-toplevel` in the working directory. If it fails, search the workspace for nested Git roots, then probe directories the request touches and their parents. If more than one repo matches, ask which one.

2. **Inspect the entire change set before choosing a message.** Run `git status --short`, then inspect the content with `git diff`, `git diff --cached`, and a summary such as `git diff HEAD --stat`. Treat tracked, staged, unstaged, deleted, renamed, and untracked files as part of the change set, and ground the message's scope in what the patches show.
3. **Read the actual patches.** For every changed path, identify what behavior, presentation, configuration, generated artifact, or documentation changed. Trace a generated file back to its source change and include that fact in the scope analysis. `git diff HEAD -- <paths>` (or equivalent) covers staged and unstaged content in one view.
4. **Structure the changes into commits.** Default to a single `git add -A` plus one commit; split into multiple commits when the changes span clearly unrelated concerns, such as separate features, fixes, or areas. When splitting, stage each group's paths separately with `git add <paths>` and commit each group in turn.
5. **Reserve the body for large commits.** Use a body only when the changes are too large to summarize in the title. Trivial or single-concern changes get a title-only message; agents tend to over-use the body on small commits, so default to no body rather than looking for reasons to add one. Every body line must stay traceable to the diff.
6. **Pass the message to Git over stdin.** Build it in PowerShell memory as an array of lines joined with LF, then pipe it to `git commit -F -`. Do not use `-m` for a multiline message (PowerShell can flatten embedded newlines in native-command arguments), and do not write a commit-message temp file:
   ```powershell
   $message = @(
     'feat(scope): description'
     ''
     'Committed-by: <model-name> on behalf of Ricardo'
   ) -join "`n"
   $message | git commit -F -
   ```
   Keep message construction and the Git command in the same PowerShell session. Git reads the complete multiline message from stdin, with no terminal quoting workaround or temp artifact.
7. **Verify every commit after it lands.** Run `git log -1 --format=%B`; run `git status --short` and confirm the intended paths are committed. Repeat both checks before retrying a commit command that exited nonzero.

Run the commits immediately; the commit request is the authorization. The user's request names the git operations to run: commits by default, plus push, tag, reset, or rebase when the user names them. To repair a malformed message, such as a stray BOM or a truncated subject, amend the message of an unpushed commit you just created, regenerating the full message, and let the amend cover that message alone.

## Rules

**Hands off the repo.** Run git commands only; don't alter any file in the repo unless the user's request says to, and don't create commit-message temp files. The working tree wins, exactly as it stands. Commit it as-is and report any surprises to the user. Use the `ask_user_question` tool to clarify the user's intent when the change set is ambiguous.

## Commit format

```
<type>(<scope>): <description>

[optional body]

Committed-by: <model-name> on behalf of Ricardo
```

- All lowercase: type, scope, and description.
- `<description>` is concise, imperative, one line.
- `<scope>` names the area the change touches; qualify it until it identifies exactly one area, separating levels with `/`.
- Leave the body out by default; add one only when the changes are too large to summarize in the title.
- The subject and body must reflect the actual complete patch, including generated artifacts.
- The trailer is unconditional: every commit made through this skill ends with it. `<model-name>` is your own model or agent name.

## Types

- feat: new feature
- fix: bug fix
- docs: documentation changes
- style: code style changes
- refactor: code refactoring
- test: adding or modifying tests
- chore: maintenance tasks
- {custom}: for anything else

## Example

```
docs(readme): correct install command

Committed-by: GLM 5.3 Flash on behalf of Ricardo
```

Title-only is the norm. A body is for large commits whose changes cannot be summarized in the title:

```
feat(server/auth): add password reset flow

- add forgot password form
- implement email verification
- add password reset endpoint

Committed-by: GLM 5.3 Flash on behalf of Ricardo
```
