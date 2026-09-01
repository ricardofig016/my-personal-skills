---
name: commit
description: Create well-formed git commits whose messages accurately summarize every committed change.
disable-model-invocation: true
---

Commit all local changes, structured into logical commits, running every git command yourself.

## Behavior

1. **Inspect the entire change set before choosing a message.** Run `git status --short`, then inspect the content with `git diff`, `git diff --cached`, and a summary such as `git diff HEAD --stat`. Treat tracked, staged, unstaged, deleted, renamed, and untracked files as part of the change set, and ground the message's scope in what the patches show.
2. **Read the actual patches.** For every changed path, identify what behavior, presentation, configuration, generated artifact, or documentation changed. Trace a generated file back to its source change and include that fact in the scope analysis. `git diff HEAD -- <paths>` (or equivalent) covers staged and unstaged content in one view.
3. **Structure the changes into commits.** Default to a single `git add -A` plus one commit; split into multiple commits when the changes span clearly unrelated concerns, such as separate features, fixes, or areas. When splitting, stage each group's paths separately with `git add <paths>` and commit each group in turn.
4. **Use a body when it completes the summary.** The body adds the major related changes beyond the subject line, with every claim traceable to the diff.
5. **Hand git the message through a file.** Write the message to a file in the OS temp directory, using the write tool or, in PowerShell, `-Encoding ascii` or `[System.IO.File]::WriteAllText`. Commit with `git commit -F <file>`, quoting the literal path used to write the file. A single-line message may go through plain `git commit -m <subject>` directly; the file route serves multiline messages.
6. **Verify every commit after it lands.** Run `git log -1 --format=%s` and confirm the subject begins at the type; run `git status --short` and confirm the intended paths are committed. Repeat both checks before retrying a commit command that exited nonzero.

Run the commits immediately; the commit request is the authorization. The user's request names the git operations to run: commits by default, plus push, tag, reset, or rebase when the user names them. To repair a malformed message, such as a stray BOM or a truncated subject, amend the message of an unpushed commit you just created, and let the amend cover that message alone.

## Commit format

```
<type>(<scope>): <description>

[optional body]
```

- All lowercase: type, scope, and description.
- `<description>` is concise, imperative, one line.
- `<scope>` names the area the change touches; qualify it until it identifies exactly one area, separating levels with `/`.
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
- {custom}: for anything else

## Example

```
feat(server/auth): add password reset flow

- add forgot password form
- implement email verification
- add password reset endpoint
```
