# pyrlyn

pyrlyn builds Rust tools for developers: installing CLI programs from GitHub releases, running
language models locally, and utilities for AI coding agents.

## Products

- [ketch](https://github.com/pyrlyn/ketch): a single-binary package manager that installs CLI
  tools and apps straight from GitHub releases on macOS, Linux and Windows, with no formulas or
  taps. It verifies the published checksum, keeps versions and removes installs cleanly.
- [rtok](https://github.com/pyrlyn/rtok): a CLI that shrinks the context of AI coding agents,
  with Claude Code hooks, an MCP server and an API proxy in one binary. Every reduction is
  measured, and the original data can be retrieved by id.
- [runa](https://github.com/pyrlyn/runa): a local-first AI runner. It checks whether your machine
  can handle a GGUF model before downloading it, runs it locally through llama.cpp, or calls
  OpenAI and Anthropic. It can also serve an OpenAI-compatible HTTP API.
- [cox](https://github.com/pyrlyn/cox): a modular terminal coding agent with a secure
  event-driven core, offering an interactive TUI, a headless mode, and editor integration through
  ACP and MCP. The project is under active development.
