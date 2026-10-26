# pyrlyn/.github — completed tasks

### T6. Add the required project files

The project was missing `AGENTS.md`, `done.md`, `roadmap.md`, `ideas.md`, and `toolchain.md`. Done means: all files exist (`toolchain.md` can be minimal — no programs beyond git).

Those files are in the repo root. `toolchain.md` lists git. The tree is guidelines and a profile README, with no executable logic, so no test was added.

### T1. Point all shared-CI references at pyrlyn/ci

`docs/ci-and-infra.md:3-5` links `pyrlyn/infra`, repeated in `README.md:4,20`, `docs/releases.md:31`, `docs/pull-requests.md:8,16`, `docs/protected-files.md:13` — but `pyrlyn/infra` is an empty repo while `pyrlyn/ci` holds everything described (consumers already pin `pyrlyn/ci` in their workflows). Done means: every reference and link names `pyrlyn/ci`, and the empty `pyrlyn/infra` repo is deleted or redirected.

pyrlyn/infra is now a real repository (the Contabo server), and no doc links it as shared CI. The 2026-10-19 audit renamed the remaining "infra" mentions in `docs/ci-and-infra.md` to pyrlyn/ci and noted that `.github/infra.yml` keeps the old name.

### T3. Correct the README's "nothing is applied yet" claim

`README.md:34-36` says no repository changes behavior because of this repo, but the root `CONTRIBUTING.md` applies as the default for every org repo lacking its own, and `profile/README.md` renders on the org page. Done means: the README states what actually applies today.

The 2026-10-19 audit rewrote `README.md` to say that `CONTRIBUTING.md` and the profile apply today, and that no templates or `SECURITY.md` exist yet (T9).

### T4. Small doc fixes batch

`docs/i18n.md:8-9` still says pyrlyn/cox#71 is open (it merged); `docs/ci-and-infra.md:28-29` lists 6 composite actions where `pyrlyn/ci` has 9 (notify-release-failure, setup-xcode, warnings-to-issues missing); `docs/installers.md:11-14` has the "No newer version available" bullet as a sibling of the Yes/No answers instead of under the Update path; `docs/languages.md` demands English-only while `profile/README.md` is Russian with no carved-out exception. Done means: all four fixed (translate the profile or add the exception).

The 2026-10-19 audit fixed the coding agent#71 status, the composite-action list (now with a link to the full set) and the installer prompt nesting. `profile/README.md` is already English.
