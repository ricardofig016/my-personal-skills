---
name: commit
description: Create well-formed git commits whose messages accurately summarize every committed change.
disable-model-invocation: true
---

Commit all local changes, structured into logical commits, running every git command yourself.

## Behavior

1. **Find the repository that owns the changes.** Run `git rev-parse --show-toplevel` in the working directory. When it fails, the session workspace is not the repo: probe the directories the request touches, their subdirectories, and their parents, and work from the first that resolves. If more than one resolves, ask the user which one. Run every later git command from the repo root.

2. **Inspect the entire change set before choosing a message.** Run `git status --short`, then inspect the content with `git diff`, `git diff --cached`, and a summary such as `git diff HEAD --stat`. Treat tracked, staged, unstaged, deleted, renamed, and untracked files as part of the change set, and ground the message's scope in what the patches show.
3. **Read the actual patches.** For every changed path, identify what behavior, presentation, configuration, generated artifact, or documentation changed. Trace a generated file back to its source change and include that fact in the scope analysis. `git diff HEAD -- <paths>` (or equivalent) covers staged and unstaged content in one view.
4. **Structure the changes into commits.** Default to a single `git add -A` plus one commit; split into multiple commits when the changes span clearly unrelated concerns, such as separate features, fixes, or areas. When splitting, stage each group's paths separately with `git add <paths>` and commit each group in turn.
5. **Use a body when it completes the summary.** The body adds the major related changes beyond the subject line, with every claim traceable to the diff.
6. **Hand git the message through a file.** Write the message from PowerShell itself (`-Encoding ascii`, `[System.IO.File]::WriteAllText`) to a fresh, uniquely named file inside the session's `$env:TEMP`, and commit with `git commit -F <file>` in the same session. That `$env:TEMP` is a per-session sandbox dir, always writable and free of stale files. The real OS temp directory is neither: a denied write there leaves any old file in place, and `git commit -F` silently commits whatever message it finds at the path.
7. **Verify every commit after it lands.** Run `git log -1 --format=%B`; run `git status --short` and confirm the intended paths are committed. Repeat both checks before retrying a commit command that exited nonzero.

Run the commits immediately; the commit request is the authorization. The user's request names the git operations to run: commits by default, plus push, tag, reset, or rebase when the user names them. To repair a malformed message, such as a stray BOM or a truncated subject, amend the message of an unpushed commit you just created, regenerating the full message, and let the amend cover that message alone.

## Rules

**Hands off the repo.** Run git commands and write the commit message to a temp file, nothing else. Don't alter any file in the repo unless the user's request says to: the working tree wins, exactly as it stands. Commit it as-is and report any surprises to the user. Use the `ask_user_question` tool to clarify the user's intent when the change set is ambiguous.

## Commit format

```
<type>(<scope>): <description>

[optional body]

Committed-by: <model-name> on behalf of Ricardo
```

- All lowercase: type, scope, and description.
- `<description>` is concise, imperative, one line.
- `<scope>` names the area the change touches; qualify it until it identifies exactly one area, separating levels with `/`.
- Add a body when it helps summarize multiple related aspects of the inspected patch.
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
feat(server/auth): add password reset flow

- add forgot password form
- implement email verification
- add password reset endpoint

Committed-by: GLM 5.3 Flash on behalf of Ricardo
```
