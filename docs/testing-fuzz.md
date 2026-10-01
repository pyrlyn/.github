# Fuzz testing

Every parser that reads untrusted or user-edited input gets a fuzz target. pyrlyn uses
[cargo-fuzz](https://github.com/rust-fuzz/cargo-fuzz) with libFuzzer (`libfuzzer-sys`). cox is
the reference setup (`fuzz/`, `.github/workflows/nightly.yml`).

## Layout

```text
fuzz/
  Cargo.toml        # package.metadata.cargo-fuzz = true, one [[bin]] per target
  Cargo.lock        # committed
  fuzz_targets/     # <target>.rs, one fuzz_target! each
  corpus/<target>/  # small seed inputs, committed
  artifacts/        # crash repros and grown corpora, git-ignored
```

- The `fuzz/` crate is not a member of the main workspace: give it an empty `[workspace]`
  table of its own (as cox does) or list it in the root `exclude`. Regular `cargo` commands and
  CI then never build it on stable.
- It depends on the workspace crates by path and is `publish = false`.
- Keep `fuzz/Cargo.lock` committed and add `/fuzz` to the cargo entry of
  `.github/dependabot.yml` (an owner change, see the [stop list](protected-files.md)).
- Commit only small seed files under `corpus/`; ignore `fuzz/artifacts/` in `.gitignore`.

## Toolchain

cargo-fuzz needs a nightly toolchain, the one toolchain not pinned in `mise.toml`. Install it
explicitly (`rustup toolchain install nightly --profile minimal`) and select it for the fuzz
steps only (`RUSTUP_TOOLCHAIN=nightly` or `cargo +nightly fuzz ...`).

## What to fuzz

- The CLI argument parser (argv): arbitrary argument vectors must produce a parse error, never a
  panic.
- Crash-prone parsers: config files, templates, tool-call payloads and streamed protocol frames
  (cox fuzzes SSE, V4A patches, front matter and permission rules), and binary headers such as
  runa's GGUF header.
- A target asserts no panic, no unbounded allocation and no hang on any input; add round-trip
  or invariant checks where the format allows them.

## In CI

- Build check: build every target on nightly (`cargo fuzz build`) so targets keep compiling
  as the code they call changes.
- Smoke run: a short `cargo fuzz run <target> -- -max_total_time=<seconds>` per target, so an
  obvious crash fails before merge.
- Long runs: a separate workflow (cox's `Nightly fuzz`, `workflow_dispatch`, 10 minutes per
  target, `fail-fast: false`) runs each target longer.
- On failure, upload `fuzz/artifacts/` as a workflow artifact (`fuzz-artifacts-<target>`).
  Triage by replaying the artifact against the target (`cargo fuzz run <target> <file>`),
  then add the minimized input to the corpus or to a regular regression test with the fix.
