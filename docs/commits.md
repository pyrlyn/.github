# Commits

## Message format

[Conventional Commits](https://www.conventionalcommits.org/), as the history of every pyrlyn
repository already uses them (`feat:`, `fix:`, `docs:`, `ci:`, `chore:`, `refactor:`,
`test:`, `perf:`, `build:`), with an optional scope: `fix(release): ...`, `ci(dependabot): ...`.

- Subject in the imperative, lowercase after the type, no trailing period:
  `feat: show a spinner while a command is working`.
- `!` after the type or scope (or a `BREAKING CHANGE:` footer) marks a breaking change.
- English only ([languages](languages.md)).
- Every line, subject and body, at most 100 characters.
- No `Co-Authored-By` trailers.
- The type drives the release: `feat` and `fix` end up in the changelog and decide the version
  bump ([releases](releases.md)), so pick it for what the change does to users, not for how
  large it is.

Some repositories enforce this locally (for example ketch's `commitlint.config.mjs`).

## History

- One logical change per commit. A refactor that makes a fix possible is its own commit before
  the fix; formatting churn never rides along with a behavior change.
- No merge commits on topic branches unless they are needed; rebase on `main` instead. Pull
  requests are squash-merged anyway ([pull requests](pull-requests.md)), so the PR title becomes
  the commit on `main` and must follow the same format.
- Never force-push a shared branch (`main`, a branch someone else works on, a branch with an
  open PR under review) without the owner's confirmation.
- No direct pushes to `main`: every change lands through a pull request.
