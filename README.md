# pyrlyn/.github

Organization-wide profile and development guidelines for the
[pyrlyn](https://github.com/pyrlyn) repositories (rtok, ketch, cox, runa, infra and the rest).

- [`profile/README.md`](profile/README.md) is the public organization profile shown on
  <https://github.com/pyrlyn>.
- [`docs/`](docs/) holds the rules every pyrlyn repository follows. A repository's own
  `AGENTS.md`, `CONTRIBUTING.md` or docs may add to them; where they disagree, the more specific
  document wins and should link back here.

## Guidelines

| Topic | Document |
| --- | --- |
| Commit messages and history | [docs/commits.md](docs/commits.md) |
| Pull requests, review and merging | [docs/pull-requests.md](docs/pull-requests.md) |
| AI-assisted code | [docs/ai-assisted-code.md](docs/ai-assisted-code.md) |
| Stop list: files other work must not touch | [docs/protected-files.md](docs/protected-files.md) |
| CI and pyrlyn/infra reusable workflows | [docs/ci-and-infra.md](docs/ci-and-infra.md) |
| Releases, version bumps, Homebrew tap | [docs/releases.md](docs/releases.md) |
| Fuzz testing | [docs/testing-fuzz.md](docs/testing-fuzz.md) |
| CLI output: colors, emoji, machine output | [docs/cli-ux.md](docs/cli-ux.md) |
| Man pages and completions | [docs/cli-completions-and-man.md](docs/cli-completions-and-man.md) |
| Install, update and uninstall behavior | [docs/installers.md](docs/installers.md) |
| Localization | [docs/i18n.md](docs/i18n.md) |
| Language of repository content | [docs/languages.md](docs/languages.md) |

See [CONTRIBUTING.md](CONTRIBUTING.md) for the short version.

## What is deliberately not here

GitHub applies some files in this repository as defaults to every repository of the
organization that lacks its own copy (issue and PR templates, `workflow-templates/`, and other
community health files). None are added yet, so no repository changes behavior because of this
repository. Add them in a separate, reviewed pull request.

`dependabot.yml` is never read from here: GitHub reads it only from each repository itself.
