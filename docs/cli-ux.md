# CLI output

Rules for what pyrlyn command-line tools print for people.

## Colors

- Errors in red, success in green, warnings in yellow.
- Color only on a terminal: when the stream is not a TTY (piped, redirected, captured by
  another program), print no escape codes.
- Honor [`NO_COLOR`](https://no-color.org/): when it is set to a non-empty value, no color.
  A `--no-color` flag, where the tool has one, also turns color off.
- Color is never the only signal: the message text still says error, warning or done.

## Emoji

- Each kind of operation may lead its human-facing lines with its own emoji (for example
  install, update, remove, done, failed), consistent across the tool.
- A config option turns emoji off; it defaults to on (`true`).

## Human output versus machine output

- Colors and emoji are for human-facing output only.
- Output meant for agents or programs (`--json` and other machine formats, output for AI
  agents, anything a script parses) is plain: no colors, no emoji, no spinners or progress
  bars, stable field names.
- Machine output goes to stdout; progress and diagnostics go to stderr.
