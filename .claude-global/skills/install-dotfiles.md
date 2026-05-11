---
name: install-dotfiles
description: Bootstrap a new machine with the user's dotfiles from github.com/peterschuellermann/dotfiles. Clones the repo, symlinks config files into $HOME, installs missing tools (zsh, vim, tmux, i3, git), and offers to set zsh as the default shell. Use when the user wants to set up a fresh machine or says "install my dotfiles".
---

Bootstrap this machine with the user's dotfiles.

## Steps

1. **Clone the repo** to `$HOME/workspace/dotfiles` if it doesn't already exist:
   ```
   git clone https://github.com/peterschuellermann/dotfiles.git "$HOME/workspace/dotfiles"
   ```
   If `$HOME/workspace` doesn't exist, create it first.

2. **Symlink dotfiles** into `$HOME` (use `ln -sf`, no backups — the user has confirmed they don't want backups):
   - `.zshrc`, `.tmux.conf`, `.vim`, `.i3` → `$HOME/`
   - `.claude-global/settings.json` → `$HOME/.claude/settings.json`
   - `.claude-global/skills` → `$HOME/.claude/skills`

   Create `$HOME/.claude` first if it doesn't exist.

3. **Detect missing tools**. Check for: `zsh`, `vim`, `tmux`, `i3`, `git`. Use `command -v <tool>` for each.

4. **Install missing tools**. `apt-get` requires root. Try `sudo apt-get install -y <pkgs>` — if it fails because sudo needs a TTY, tell the user to run it themselves prefixed with `!` so the output lands in this session:
   ```
   ! sudo apt-get install -y <missing-packages>
   ```
   On non-Debian systems, adapt the package manager (`dnf`, `pacman`, `brew`, etc.) — detect via `command -v apt-get || command -v dnf || ...`.

5. **Offer to set zsh as the default shell** via `chsh -s "$(command -v zsh)"`. This will likely need the user's password — if `chsh` fails with PAM auth, ask the user to run `! chsh -s /usr/bin/zsh` themselves.

## Notes

- Do not back up existing dotfiles before symlinking; the user has explicitly said no backups.
- The repo's `.claude-global/` directory maps to `~/.claude/` (Claude Code global config), not directly under `$HOME`.
- The `.vim/bundle/` directory contains git submodules (Vundle plugins). If `git clone` doesn't pull them, run `git submodule update --init --recursive` inside the repo.
