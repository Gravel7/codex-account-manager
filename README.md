# codex-account-manager

A small wrapper for running Codex CLI with multiple isolated accounts using `CODEX_HOME`.

## Why

Codex CLI authentication is tied to the active `CODEX_HOME`. This tool gives you account-scoped homes and quick switching without manual env juggling.

## Features

- `list`: list known account homes and login summary
- `current`: show selected account and resolved `CODEX_HOME`
- `switch`: change the selected account
- `login/logout`: run Codex auth flow against a specific account home
- `run/exec`: run Codex commands with the selected account home

## Install

```bash
git clone git@github.com:Gravel7/codex-account-manager.git
cd codex-account-manager
chmod +x bin/codex-account
```

Optional: add to PATH in `~/.zshrc`:

```bash
export PATH="$PATH:$HOME/path/to/codex-account-manager/bin"
```

## Usage

```bash
codex-account init work --from-current-config
codex-account login work
codex-account switch work
codex-account current
codex-account run -- --help
```

## zsh Convenience Function

Add to `~/.zshrc`:

```zsh
cx() {
  command codex-account "$@"
}

codex() {
  command codex-account run -- "$@"
}
```

After this:

- `cx switch work`
- `codex` always runs under the currently selected account

## Environment Variables

- `CODEX_ACCOUNT_ROOT`: override account directories root (default: `$HOME`)
- `CODEX_ACCOUNT_STATE_ROOT`: override switch-state directory (default: `~/.codex-account-manager`)
- `CODEX_ACCOUNT`: force account name for one command

## Account Directory Convention

- `default` -> `~/.codex`
- `<name>` -> `~/.codex-<name>`

## Security Notes

- This tool reads `auth.json` only to show summary (`auth_mode`, `account_id`, `email`).
- It never prints raw `access_token` or `refresh_token`.

## Verify Active Account

Use both checks:

```bash
codex-account current
env | grep '^CODEX_HOME='
```

At runtime, the actual account is determined by `CODEX_HOME` used to launch `codex`.
