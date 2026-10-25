# Releases

pyrlyn binaries are released as a git tag plus a GitHub Release with prebuilt archives, built by
[cargo-dist](https://github.com/axodotdev/cargo-dist)'s generated `release.yml` (the token CLI, the package manager,
runa) or a hand-written `release.yml` (the coding agent). The release flow does not publish the binaries to
crates.io: `release-plz.toml` sets `publish = false`.

## Principles

- A tag exists only if the release exists. In the token CLI, the package manager and the coding agent the release workflow creates
  the tag when it publishes the Release, after every target has built, so a failed build leaves
  `main` untagged, and release-plz does not create the Release (`git_release_enable = false`).
  runa is the exception so far: its release script pushes the tag, and the tag push starts
  dist.
- The version lives in `Cargo.toml` and is changed only by the release tooling (the release-plz
  PR or the repository's release script), never by hand in an ordinary commit.
- What ships is what was tested: the release is verified on the release commit before it is
  built.
- Release workflows never cancel mid-run ([concurrency](ci-and-infra.md#concurrency)).

## release-plz flow

1. On every push to `main`, release-plz keeps one release pull request open with the next
   version and the `CHANGELOG.md` entry (git-cliff config in `cliff.toml` where present).
2. Merging that PR is the release. The merge commit is recognised by its subject, which must
   match `pr_name` in `release-plz.toml` (`release: v{{ version }}` in the token CLI,
   `chore: release v{{ version }}` in the package manager).
3. The merge runs the verify command on the merge commit, then dispatches the release workflow
   with `tag=v<version>`.

The token CLI and the coding agent call pyrlyn/ci's `release-plz.yml`; the package manager still has a local copy. It needs
`RELEASE_PLZ_TOKEN` (organization secret, fine-grained PAT with contents and pull requests
write): a release PR opened with `GITHUB_TOKEN` would run no CI.

## Bump workflow

`Bump and release` (`bump.yml`, `workflow_dispatch`) is the one-click alternative in the token CLI,
The package manager, the coding agent and runa; each calls pyrlyn/ci's `bump.yml`. It takes a `level` input (`patch`,
`minor`, `major`, default `patch`) and runs the repository's release script
(`tools/release.sh` in the token CLI, `scripts/release.sh` in the package manager, the coding agent and runa):

- The version in `Cargo.toml` is released as it stands if it has no tag yet. It is raised by
  `level` only when that version is already tagged.
- In the token CLI and the package manager the script lands one version commit on `main` (only `Cargo.toml`,
  `Cargo.lock` and `CHANGELOG.md`) and dispatches the release workflow; runa's pushes the
  commit and the tag. Nobody types a version number.
- `--dry-run` prints the version that would be released and changes nothing. The token CLI's and
  The package manager's workflows deliberately have no dry-run input (a green run must mean a release), so
  the preview is local only.

## Version bump semantics

Versions follow SemVer and are derived from Conventional Commits since the last tag:
`fix:` raises the patch, `feat:` the minor, a breaking change (`!` or `BREAKING CHANGE:`) the
major. Below 1.0, release-plz by default sends `feat:` to the patch; the package manager overrides that with
`features_always_increment_minor = true`. Choose commit types accordingly
([commits](commits.md)).

## Homebrew tap

Formulae and casks live in [pyrlyn/homebrew-tap](https://github.com/pyrlyn/homebrew-tap). A
release is complete only when the tap points at it:

- The package manager and the coding agent regenerate their cask (`Casks/<name>.rb`: version and the macOS arm64 checksum)
  after the Release is published, check it with `brew style`, smoke-test it, and push it to the
  tap. This needs a `HOMEBREW_TAP_TOKEN` that can push to pyrlyn/homebrew-tap; without it the
  job fails instead of silently leaving the tap behind. Repair a failed tap update without a
  new release: re-run the tap job from the release run (the package manager) or run
  `gh workflow run release.yml -f force=true` (the coding agent).
- The token CLI's dist build attaches `the token CLI.rb` to the Release but does not push to the tap. The tap's
  own `sync-rtok.yml` downloads it and opens a pull request there; merging it publishes.
- runa's dist config names the shared `pyrlyn/homebrew-tap` and keeps the Homebrew publish job
  off until runa's formula is wired into that tap.
