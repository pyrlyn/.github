# pyrlyn/.github

<https://github.com/pyrlyn/.github>

Org-level default repository for the pyrlyn organization: the public org profile README and org-wide development guideline docs (commits, PRs, CI, releases, fuzzing, CLI UX, installers, i18n, languages, protected files) plus CONTRIBUTING.md.

| # | Status | Priority | Complexity | Readiness | Agent |
| --- | --- | --- | --- | --- | --- |
| T1 | todo | P1 | 1 | 0% | |
| T2 | todo | P2 | 2 | 0% | |
| T3 | todo | P2 | 1 | 0% | |
| T4 | todo | P3 | 1 | 0% | |
| T5 | todo | P3 | 1 | 0% | |

Audit note (2026-10-07): the checkout sits on branch `docs/dev-guidelines`, not main; rebase findings onto main before working. Verified via the GitHub API: no workflows exist in this repo, so there is no workflow security surface here.

### T1. Point all shared-CI references at pyrlyn/ci

`docs/ci-and-infra.md:3-5` links `pyrlyn/infra`, repeated in `README.md:4,20`, `docs/releases.md:31`, `docs/pull-requests.md:8,16`, `docs/protected-files.md:13` — but `pyrlyn/infra` is an empty repo while `pyrlyn/ci` holds everything described (consumers already pin `pyrlyn/ci` in their workflows). Done means: every reference and link names `pyrlyn/ci`, and the empty `pyrlyn/infra` repo is deleted or redirected.

### T2. Fix stale workflow facts in the release/CI docs

`docs/ci-and-infra.md:42-44` lists cox among dist-generated repos (it has a hand-written `release.yml`, no `build-setup.yml`); `docs/releases.md:39` names runa's nonexistent `release-manual.yml` (it has `bump.yml`); `releases.md:37` omits cox's `bump.yml`; `ci-and-infra.md:45` names ketch's nonexistent `verify.yml`; `releases.md:72` says runa's dist tap is `listepo/homebrew-runa` (404) when `dist-workspace.toml` says `pyrlyn/homebrew-tap`; `releases.md:70-71` describes the tap's `sync-rtok.yml` which no longer exists. Done means: every statement about a repo's workflows matches that repo's actual `.github/workflows/`.

### T3. Correct the README's "nothing is applied yet" claim

`README.md:34-36` says no repository changes behavior because of this repo, but the root `CONTRIBUTING.md` applies as the default for every org repo lacking its own, and `profile/README.md` renders on the org page. Done means: the README states what actually applies today.

### T4. Small doc fixes batch

`docs/i18n.md:8-9` still says pyrlyn/cox#71 is open (it merged); `docs/ci-and-infra.md:28-29` lists 6 composite actions where `pyrlyn/ci` has 9 (notify-release-failure, setup-xcode, warnings-to-issues missing); `docs/installers.md:11-14` has the "No newer version available" bullet as a sibling of the Yes/No answers instead of under the Update path; `docs/languages.md` demands English-only while `profile/README.md` is Russian with no carved-out exception. Done means: all four fixed (translate the profile or add the exception).

### T5. State the secrets tradeoff of org-repo branches

`docs/pull-requests.md:5-6` directs contributors to push branches into the org repo (not forks) so CI runs with repo secrets; that deliberately exposes secrets to everyone with write access. Done means: the doc says this tradeoff explicitly.
