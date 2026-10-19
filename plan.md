# pyrlyn/.github

<https://github.com/pyrlyn/.github>

Org-level default repository for the pyrlyn organization: the public org profile README and org-wide development guideline docs (commits, PRs, CI, releases, fuzzing, CLI UX, installers, i18n, languages, protected files) plus CONTRIBUTING.md.

| # | Status | Priority | Complexity | Readiness | Agent |
| --- | --- | --- | --- | --- | --- |
| T2 | todo | P2 | 2 | 40% | |
| T5 | todo | P3 | 1 | 0% | |
| T7 | todo | P2 | 2 | 0% | |
| T8 | todo | P3 | 1 | 0% | |
| T9 | todo | P3 | 1 | 0% | |

Audit note (2026-10-07): no workflows exist in this repo, so there is no workflow security surface
here (still true on 2026-10-19).

### T2. Fix stale workflow facts in the release/CI docs

`docs/ci-and-infra.md:42-44` lists cox among dist-generated repos (it has a hand-written `release.yml`, no `build-setup.yml`); `docs/releases.md:39` names runa's nonexistent `release-manual.yml` (it has `bump.yml`); `releases.md:37` omits cox's `bump.yml`; `ci-and-infra.md:45` names ketch's nonexistent `verify.yml`; `releases.md:72` says runa's dist tap is `listepo/homebrew-runa` (404) when `dist-workspace.toml` says `pyrlyn/homebrew-tap`; `releases.md:70-71` describes the tap's `sync-rtok.yml` which no longer exists. Done means: every statement about a repo's workflows matches that repo's actual `.github/workflows/`.

Audit 2026-10-19: the `ci-and-infra.md` dist list, ketch `verify.yml` and runa `release-manual.yml`
are fixed. Still stale: no repository calls pyrlyn/ci's `release-plz.yml` any more (all four use
`bump.yml`), so `releases.md:10-14,21-33` and `pull-requests.md:28-31` describe a flow nobody
runs, and cox has no `release-plz.toml` although `releases.md:5-6` implies every repo has one;
`releases.md:70-71` names the tap's `sync-rtok.yml`, but pyrlyn/homebrew-tap has no workflows.
Severity: low.

### T5. State the secrets tradeoff of org-repo branches

`docs/pull-requests.md:5-6` directs contributors to push branches into the org repo (not forks) so CI runs with repo secrets; that deliberately exposes secrets to everyone with write access. Done means: the doc says this tradeoff explicitly.

## Audit 2026-10-19

Three passes over `main` at 07913ea: (1) module walk, (2) trust boundaries (what this repo
applies to other repos: community-health defaults and the Renovate preset), (3) docs against the
live repositories. Fixed in the audit PR (branch `fix/dotgithub-audit-2026-10-19`), docs only:
the "infra" name for pyrlyn/ci and its action list (closes T1), dist and release-workflow facts
(part of T2), cox#71, the installer prompt nesting, and the README community-health claim (closes
T3 and T4; the profile is English now).

### T7. Settle the pyrlyn/ci pinning rule

`docs/ci-and-infra.md:50-61` (and pyrlyn/ci's README) say to reference pyrlyn/ci by full commit
SHA and never `@main`, and that Dependabot moves the pins. pyrlyn/ci's `no-pinned-ci.yml` now fails
any pull request that pins pyrlyn/ci ("the reference is always `@main`"), and `pin-sync.yml` and
Renovate exist alongside. A contributor following this doc gets a red check; following the check
means every push to pyrlyn/ci `main` runs immediately, with secrets, in every consumer. Severity:
medium (policy contradiction with supply-chain impact). Done means: the owner picks one rule, and
this doc, pyrlyn/ci's README and the check agree; if `@main` stays, the doc states the tradeoff and
that pyrlyn/ci `main` needs branch protection and review.

### T8. Make the Renovate preset match the pinning rules

`renovate-config.json` enables `github-actions` without `helpers:pinGitHubActionDigests`, so
Renovate keeps tag references even though `action-pins.yml` and this repo's docs require full SHAs
for external actions. `enabledManagers` also leaves out `pep621`/`uv` and `ansible-galaxy`, so
pyrlyn/infra's `uv.lock` and `collections/requirements.yml` never get updates. Severity: low.
Done means: digest pinning on for external actions (respecting T7 for pyrlyn/ci) and the managers
that the org's repositories actually use, with `docs/renovate.md:16-18` updated. Merging it opens
PRs across the org, so it needs the owner's go-ahead.

### T9. Add an org-wide security policy

There is no `SECURITY.md`, so no pyrlyn repository without its own shows a private way to report
a vulnerability. Severity: low. Done means: a `SECURITY.md` (GitHub private vulnerability
reporting or a contact), added in its own reviewed PR as `README.md` asks, and listed there.
