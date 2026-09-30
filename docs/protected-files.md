# Protected files (stop list)

Some files are owned by the docs sync or by the maintainers. Other work, CI migrations
included, must not edit them, even when a change there looks like the obvious fix. Report the
needed change to the owner instead.

- `.github/workflows/sync-docs.yml` - owned by the docs sync. Publishes the repository's
  `docs/` to pyrlyn/landing with the `SITE_DEPLOY_KEY` deploy key.
- `.github/dependabot.yml` - owned by the maintainers: update schedule, groups, cooldowns and
  ignores.
- `.github/workflows/dependabot.yml`, the Dependabot auto-merge caller - owned by the
  maintainers: it decides which Dependabot PRs merge unattended (patch updates after green CI).
  rtok, ketch and cox each keep a local copy today; pyrlyn/infra's `dependabot-automerge.yml`
  is the shared version. Moving a repository to it is also the owner's change.

Rules:

- Migration or refactoring work never merges Dependabot PRs. They go through the repository's
  auto-merge flow or a maintainer's review.
- A change to one of these files is its own pull request, reviewed by the owner; it is never
  bundled into other work.
- Example: cox pins `wasmtime` to the major version extism works with (`Cargo.toml`), so
  Dependabot's wasmtime major bumps need an `ignore` entry in `.github/dependabot.yml`. The
  owner adds it; a PR that only needed CI to pass does not.
