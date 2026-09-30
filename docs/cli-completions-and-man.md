# Man pages and shell completions

Every pyrlyn CLI ships reference documentation and completions for all of its commands and
subcommands.

## Man pages

- A man page in roff for the tool and for every subcommand (`<tool>.1`, `<tool>-<command>.1`).
- Installed where the platform looks for them by the installer or package, and available from
  the tool itself (e.g. a `man` subcommand that writes the pages to a directory).

## Completions

- bash: completion for every command, subcommand, flag and, where possible, flag value.
- PowerShell: a script built on `Register-ArgumentCompleter -Native -CommandName <tool>`.
- Windows `cmd`: `cmd` has no programmable completion, so ship `doskey` macros for the common
  commands instead.
- Other shells the generator supports (zsh, fish) come along at no extra cost; ship them too.
- Expose them from the tool itself (e.g. `<tool> completions <shell>`) so a user can regenerate
  them after an update.

## Generate, don't hand-write

Generate man pages and completions from the CLI definition, so they cannot drift from the
parser. With clap, use [`clap_mangen`](https://crates.io/crates/clap_mangen) for roff and
[`clap_complete`](https://crates.io/crates/clap_complete) for completions (rtok's
`rtok man` and `rtok completions`). Hand-write only what no generator covers, such as the
`doskey` macros, and test that every command appears in each output.
