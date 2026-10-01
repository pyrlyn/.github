# Pull requests

## Opening

- Push the branch to the pyrlyn repository itself (`pyrlyn/<repo>`), not to a fork, so CI runs
  with the repository's settings and secrets.
- One pull request per repository per change. A change that touches several repositories (for
  example repinning pyrlyn/infra) is one PR in each, each complete on its own.
- Prefer small, focused PRs; split unrelated changes.
- Title in Conventional Commits form ([commits](commits.md)); it becomes the squash commit on
  `main`.
- The description says what changed and why, and links related issues, PRs and workflow runs.
- A PR that fixes a known breakage says so and links the PR or run that broke it.
- If code in the PR was written with AI assistance, see [AI-assisted code](ai-assisted-code.md).
- English, lines of at most 100 characters, no `Co-Authored-By` in commits.
- Open as a draft while work is in progress: pyrlyn/infra's `pipeline.yml` skips every job,
  including the gate, on draft PRs.

## Review

- CodeRabbit runs on demand only: the repositories set `reviews.auto_review.enabled: false` in
  `.coderabbit.yaml`. Ask for a review with a `@coderabbitai review` comment when you want one.
- Dependabot PRs and changes to the [stop list](protected-files.md) are handled separately and
  are never merged without review.

## Merging

- Squash merge only.
- CI must be green on the head commit. The only exception is a failure already known and
  already fixed on `main` (say which one, with a link, in the PR).
- Merge with `gh pr merge --squash --match-head-commit <sha>`, so nothing pushed after the last
  check is merged unseen.
- Never `--auto` and never `--admin`: no merge that bypasses the checks or a review.
- No direct pushes to `main`.
- No force-push to a shared branch without the owner's confirmation.

## Branches and worktrees

- Do not delete a branch, local or remote, unless explicitly asked to.
- Work in a separate git worktree per task and lock it (`git worktree lock --reason ...`) so
  another person or agent does not remove it while it is in use.
