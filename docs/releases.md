# Releases

pyrlyn binaries are released as a git tag plus a GitHub Release with prebuilt archives, built by
[cargo-dist](https://github.com/axodotdev/cargo-dist)'s generated `release.yml` (rtok, ketch,
runa) or a hand-written `release.yml` (cox). The release flow does not publish the binaries to
crates.io: `release-plz.toml` sets `publish = false`.

## Principles

- A tag exists only if the release exists. In rtok, ketch and cox the release workflow creates
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
   match `pr_name` in `release-plz.toml` (`release: v{{ version }}` in rtok,
   `chore: release v{{ version }}` in ketch).
3. The merge runs the verify command on the merge commit, then dispatches the release workflow
   with `tag=v<version>`.

rtok and cox call pyrlyn/infra's `release-plz.yml`; ketch still has a local copy. It needs
`RELEASE_PLZ_TOKEN` (organization secret, fine-grained PAT with contents and pull requests
write): a release PR opened with `GITHUB_TOKEN` would run no CI.

## Bump workflow

`Bump and release` (`bump.yml`, `workflow_dispatch`) is the one-click alternative in rtok and
ketch; runa has the equivalent `release-manual.yml`. It takes a `level` input (`patch`,
`minor`, `major`, default `patch`) and runs the repository's release script
(`tools/release.sh` in rtok, `scripts/release.sh` in ketch and runa):

- The version in `Cargo.toml` is released as it stands if it has no tag yet. It is raised by
  `level` only when that version is already tagged.
- In rtok and ketch the script lands one version commit on `main` (only `Cargo.toml`,
  `Cargo.lock` and `CHANGELOG.md`) and dispatches the release workflow; runa's pushes the
  commit and the tag. Nobody types a version number.
- `--dry-run` prints the version that would be released and changes nothing. rtok's and
  ketch's workflows deliberately have no dry-run input (a green run must mean a release), so
  the preview is local only.

## Version bump semantics

Versions follow SemVer and are derived from Conventional Commits since the last tag:
`fix:` raises the patch, `feat:` the minor, a breaking change (`!` or `BREAKING CHANGE:`) the
major. Below 1.0, release-plz by default sends `feat:` to the patch; ketch overrides that with
`features_always_increment_minor = true`. Choose commit types accordingly
([commits](commits.md)).

## Homebrew tap

Formulae and casks live in [pyrlyn/homebrew-tap](https://github.com/pyrlyn/homebrew-tap). A
release is complete only when the tap points at it:

- ketch and cox regenerate their cask (`Casks/<name>.rb`: version and both macOS checksums)
  after the Release is published, check it with `brew style`, smoke-test it, and push it to the
  tap. This needs a `HOMEBREW_TAP_TOKEN` that can push to pyrlyn/homebrew-tap; without it the
  job fails instead of silently leaving the tap behind. Repair a failed tap update without a
  new release: re-run the tap job from the release run (ketch) or run
  `gh workflow run release.yml -f force=true` (cox).
- rtok's dist build attaches `rtok.rb` to the Release but does not push to the tap. The tap's
  own `sync-rtok.yml` downloads it and opens a pull request there; merging it publishes.
- runa's dist config names `listepo/homebrew-runa` and keeps the Homebrew publish job off.
