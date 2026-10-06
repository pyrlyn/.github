# CI and pyrlyn/ci

Shared CI lives in [pyrlyn/ci](https://github.com/pyrlyn/ci). Its
[README](https://github.com/pyrlyn/ci#readme) and
[docs/reusable-workflows.md](https://github.com/pyrlyn/ci/blob/main/docs/reusable-workflows.md)
are the reference for inputs, secrets and permissions; this page is the standard a repository
follows when it moves its CI there.

## What lives in pyrlyn/ci

Reusable workflows (`on: workflow_call`, under `.github/workflows/`):

- `ci.yml` - single, config-driven entrypoint (`.github/infra.yml` in the caller): Rust CI,
  scans, SonarCloud, lint and repository-specific jobs behind a `gate`.
- `pipeline.yml` - `ci-rust.yml` + CodeQL + Semgrep + Snyk in parallel behind a `gate`.
- `ci-rust.yml` - fmt, clippy, check, tests on a shared four-target matrix, optional MSRV.
  macOS is arm64 (Apple Silicon, `aarch64-apple-darwin`) only; Intel Macs are unsupported.
- `ci-dotnet.yml`, `changes.yml` - .NET tests and changed-file classification by ecosystem.
- `lint.yml` - actionlint (+ shellcheck) on the caller's workflows.
- `codeql.yml`, `semgrep.yml`, `snyk.yml`, `sonarcloud.yml` - scans.
- `release-plz.yml` - release PR; on its merge, verify and dispatch the release workflow.
- `release.yml` - manual release for repositories not built with cargo-dist.
- `bump.yml` - verify, then run the repository's release script (bump + dispatch).
- `pages.yml` - build a static site and deploy it to GitHub Pages.
- `sync-docs.yml` - publish `docs/` to pyrlyn/landing.
- `dependabot-automerge.yml` - merge allowed Dependabot updates after green CI.
- `revert-on-failure.yml` - revert a failed push to the default branch.

Composite actions (`.github/actions/`): `gate`, `cancel-run`, `changes`, `config`,
`macos-sign`, `revert-on-failure`.

infra's own triggered workflows check infra itself: `action-pins.yml` (fails on any external
action or reusable workflow not pinned to a full commit SHA), `lint.yml`, and `self-test.yml`
(runs the reusable workflows against a fixture crate before any caller pins a change).

## What stays in each repository

- Thin caller workflows: triggers, `concurrency`, job `permissions`, explicit `secrets:` and a
  `uses:` of the infra workflow. No build logic in the caller.
- `.github/infra.yml` for `ci.yml` settings, where the repository uses it.
- `.github/dependabot.yml` (GitHub reads it only from the repository itself).
- Repository-specific jobs, e.g. rtok's webui/wasm and plugin-version checks.
- cargo-dist's generated `release.yml` and `build-setup.yml` in repositories built with dist
  (rtok, ketch, runa, cox): dist regenerates `release.yml` and fails `dist plan` on a
  hand-edited copy.
- Release glue that dist calls, e.g. ketch's `tap.yml` and `verify.yml`.
- The files on the [stop list](protected-files.md), which migration work does not touch.

## Pinning

Reference reusable workflows and actions by full commit SHA, with a comment naming the ref:

```yaml
uses: pyrlyn/ci/.github/workflows/ci.yml@<40-char sha> # main 2026-09-27
```

Dependabot (`package-ecosystem: github-actions`) moves SHA pins of reusable workflows like
action pins, by pull request. Never reference `@main` or a tag.

## Concurrency

Only the newest run of a workflow on a ref should execute. The convention:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true # CI, lint, scans
```

- CI, lint and scan workflows cancel older runs (`cancel-in-progress: true`). pyrlyn/ci's
  own `action-pins.yml`, `lint.yml` and `self-test.yml` use exactly the block above.
- Callers whose `main` runs feed `revert-on-failure` keep every push's run on `main` and
  cancel only on pull requests, so the revert hits the push that failed. infra documents this
  variant and the consumers' `pipeline.yml` callers use it:

  ```yaml
  concurrency:
    group: ${{ github.workflow }}-${{ github.event.pull_request.number || github.ref }}
    cancel-in-progress: ${{ github.event_name == 'pull_request' }}
  ```

- Bump, release-plz and release workflows never cancel mid-run (`cancel-in-progress: false`):
  they push a version commit or dispatch publishing, and a half-cancelled run can leave a
  version with no release. A newer run waits instead.
- Put `concurrency` in the thin caller only. A reusable workflow sees the caller's `github`
  context, so a group built there from `github.workflow` collides with the caller's own group.

## Secrets

- Secrets shared by several repositories are organization secrets, not per-repository copies.
  Today `RELEASE_PLZ_TOKEN` and `CARGO_REGISTRY_TOKEN` are organization secrets available to
  all repositories.
- After a repository moves to an organization secret, delete its repository-level copy. A
  repository secret with the same name silently overrides the organization one, so a leftover
  copy has to be rotated on its own and drifts from the shared token.
- Pass secrets to reusable workflows explicitly in `secrets:`, not with `secrets: inherit`.
- Secrets that are per repository by nature (e.g. `SITE_DEPLOY_KEY`, the private half of a
  deploy key on pyrlyn/landing) stay repository secrets.

## Permissions

Workflows start from `permissions: contents: read` at the top. Each calling job grants what
the called workflow needs (see infra's table), including `actions: write` for `cancel-run`:
GitHub refuses to start a nested job that asks for more than its caller grants.
