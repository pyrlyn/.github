# Renovate

pyrlyn uses the hosted [Mend Renovate GitHub App](https://github.com/apps/renovate), which is
free for open-source and public repositories (no paid plan).

`renovate-config.json` in this repository is the shared preset. A repository opts in with a
`renovate.json` in its root:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>pyrlyn/.github:renovate-config"]
}
```

The preset enables the cargo, github-actions, npm and mise managers, keeps automerge off
(`automerge` and `platformAutomerge` are `false`), runs once a week (Monday before 06:00,
Europe/Kiev) and allows 3 open pull requests at a time. Updates younger than 7 days wait.

Hosted Renovate cannot refresh `mise.lock` (it needs an unsafe execution only self-hosted
setups may allow); it still bumps the versions in `mise.toml`.
