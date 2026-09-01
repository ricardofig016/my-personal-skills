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
5. **Hand git the message through a file, never through the shell.** Inline multiline messages (`-m` with a here-string, heredoc, or embedded newlines) are the most common way a commit lands mangled or not at all: shell quoting can hand git a pathspec instead of a message. Write the message to a file outside the working tree — the OS temp directory, since a file inside the tree can be swept up by `git add -A` — then commit with `git commit -F <file>`. On Windows PowerShell, `Set-Content -Encoding utf8` prepends a UTF-8 BOM that becomes the first character of the subject; write the file with the write tool, `-Encoding ascii`, or `[System.IO.File]::WriteAllText` instead. A single-line subject with no body may go through a plain `-m` directly — the file rule exists for multiline bodies, where shell argument splitting is what breaks.
6. **Verify every commit after it lands, and after any failure.** Run `git log -1 --format=%s` and confirm the subject begins at the type with no stray leading characters, and `git status --short` to confirm the intended paths are committed. If a commit command exits nonzero, check both before retrying: a failed attempt can leave paths staged with nothing committed.

Run the commits immediately. Do not ask for confirmation. Do not push, tag, reset, rebase, or touch the remote unless the user explicitly asks for that operation. The one exception, to repair your own work: you may `git commit --amend` the message of a commit you yourself just created and that has not been pushed, solely to fix a malformed message such as a stray BOM or truncated subject — never amend its content, and never amend anything else.

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
