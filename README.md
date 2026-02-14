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
```

Option A (user-level, no `sudo`):

```bash
mkdir -p ~/.local/bin
install -m 755 ./bin/codex-account ~/.local/bin/codex-account
```

Add to `~/.zshrc`:

```zsh
export PATH="$HOME/.local/bin:$PATH"
```

Option B (global, with `sudo`):

```bash
sudo install -m 755 ./bin/codex-account /usr/local/bin/codex-account
```

Apply and verify:

```bash
source ~/.zshrc
rehash
which codex-account
codex-account --help
```

If `command not found` after Option B, ensure `/usr/local/bin` is in PATH:

```zsh
echo $PATH | tr ':' '\n' | grep -x /usr/local/bin
```

If missing, add this to `~/.zshrc`:

```zsh
export PATH="/usr/local/bin:$PATH"
```

## Usage

```bash
codex-account init work --from-current-config
codex-account login work
codex-account switch work
codex-account current
codex-account run -- --help
```

## zsh Notes

- Recommended wrapper (do not override `codex`):

```zsh
cx() { command codex-account "$@"; }
```

- Keep native `codex` unchanged. Do not define `codex() { ... }`.
- `cx switch work` sets the selected account in state.
- Runtime account is determined by `CODEX_HOME` used when `codex` starts.
- If you previously defined a `codex()` function, remove it and reload shell:

```zsh
unset -f codex 2>/dev/null
source ~/.zshrc
rehash
```

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
