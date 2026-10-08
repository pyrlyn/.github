# AGENTS.md

If an AGENTS.md or CLAUDE.md exists higher in the tree, follow it too; on conflict, ask the creator.

## What this repo is

The organization repository for [pyrlyn](https://github.com/pyrlyn). This checkout's `origin` is `https://github.com/pyrlyn/.github.git`.

| Path | Role |
| --- | --- |
| `profile/README.md` | Public organization profile rendered on https://github.com/pyrlyn |
| `docs/` | Rules every pyrlyn repository follows: commits, pull requests, AI-assisted code, protected files, CI, releases, fuzzing, CLI UX, completions and man pages, installers, i18n, languages |
| `CONTRIBUTING.md` | Short version of `docs/` |

A repository's own `AGENTS.md`, `CONTRIBUTING.md`, or docs may add to these. Where they disagree, the more specific document wins.

The README states that GitHub default community-health files (issue and PR templates, `workflow-templates/`) are not in this repo yet, and that `dependabot.yml` is never read from an org `.github` repo.

## Tests

Markdown and a profile README only. There is no workflow, script, or schema checker. Do not add a test framework.
