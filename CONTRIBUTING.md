# Contributing to pyrlyn repositories

The short version of [docs/](docs/). Each repository's own `CONTRIBUTING.md` covers how to
build and test it.

- Write everything in English ([languages](docs/languages.md)).
- Commits follow Conventional Commits, one logical change each, lines of at most 100
  characters, no `Co-Authored-By` trailers ([commits](docs/commits.md)).
- Open a pull request from a branch of the repository; never push to `main` directly. Keep it
  small and focused, and say what changed and why ([pull requests](docs/pull-requests.md)).
- If code in a pull request was written with AI assistance, the PR description must say so.
  This applies to anyone who is not a member of the pyrlyn organization; organization members
  are exempt ([AI-assisted code](docs/ai-assisted-code.md)).
- Pull requests are squash-merged once CI is green; release version-bump PRs are the one
  exception ([merging](docs/pull-requests.md#merging)).
- Do not edit the files on the [stop list](docs/protected-files.md) (`sync-docs.yml`,
  `dependabot.yml`, Dependabot auto-merge) as part of other work.
