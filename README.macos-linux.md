# macOS/Linux Zsh Setup

This guide configures the Starship prompt and optional Zsh plugins for one macOS/Linux account. Normal users and root accounts can keep separate binaries, plugins, and shell configuration files.

## 1. Install Starship

Install Starship with the official installer:

```bash
curl -sS https://starship.rs/install.sh | sh
```

For more installation options and shell-specific details, see the [official Starship guide](https://starship.rs/guide/).

> [!TIP]
> If you prefer an account-local binary instead of a system-wide install, use:
>
> ```bash
> mkdir -p "$HOME/.local/bin"
> curl -sS https://starship.rs/install.sh | sh -s -- -b "$HOME/.local/bin"
> ```
>
> Then make sure `~/.local/bin` is on `PATH` before initializing Starship.

## 2. Clone this repository

```bash
git clone https://github.com/HungNth/starship-config.git
cd starship-config
```

All commands below assume your terminal is currently open at the root of the cloned repository.

## Files in this repository

```text
starship-config/
├── README.md
├── README.macos-linux.md
├── README.windows.md
└── starship.toml
```

`starship.toml` is the default Starship config template. Copy it to the current account's Starship config path during setup.

## 3. Back up existing files

Run these steps as the account that will use the prompt. If you want this setup for another account, log in as that account and repeat the same steps there.

```bash
mkdir -p "$HOME/.config"
[ -f "$HOME/.config/starship.toml" ] && cp "$HOME/.config/starship.toml" "$HOME/.config/starship.toml.bak.$(date +%Y%m%d%H%M%S)"
[ -f "$HOME/.zshrc" ] && cp "$HOME/.zshrc" "$HOME/.zshrc.bak.$(date +%Y%m%d%H%M%S)"
[ -f "$HOME/.zshenv" ] && cp "$HOME/.zshenv" "$HOME/.zshenv.bak.$(date +%Y%m%d%H%M%S)"
```

## 4. Copy the Starship config

From the repository root, run:

```bash
mkdir -p "$HOME/.config"
cp starship.toml "$HOME/.config/starship.toml"
```

### Optional: darker color for a root account

If the current account is `root`, use the same setup steps above from a root shell. After copying `starship.toml`, edit the copied file at:

```text
$HOME/.config/starship.toml
```

The default template uses `red` for the OS and username segment. In the Catppuccin Macchiato palette, the previous `maroon` color is visually lighter than `red`, so a darker custom color is easier to notice for a root prompt.

In `~/.config/starship.toml`, replace the prompt segment color names in the `format` block:

```toml
[](red)\
[](bg:peach fg:red)\
```

with:

```toml
[](root_red)\
[](bg:peach fg:root_red)\
```

Then update the OS and username styles:

```toml
[os]
style = "bg:root_red fg:crust"

[username]
style_user = "bg:root_red fg:crust"
style_root = "bg:root_red fg:crust"
```

Finally, add the darker custom color to the active palette section:

```toml
[palettes.catppuccin_macchiato]
root_red = "#c43e4f"
```

`style_root` controls the username style when the account is root. Updating `style_user` too is fine when the copied config belongs only to the root account.

## 5. Install Zsh plugins for this account

```bash
mkdir -p "$HOME/.zsh/plugins"

git clone --depth 1 -- https://github.com/zsh-users/zsh-autosuggestions.git "$HOME/.zsh/plugins/zsh-autosuggestions"
git clone --depth 1 -- https://github.com/zsh-users/zsh-syntax-highlighting.git "$HOME/.zsh/plugins/zsh-syntax-highlighting"
git clone --depth 1 -- https://github.com/marlonrichert/zsh-autocomplete.git "$HOME/.zsh/plugins/zsh-autocomplete"
```

If a plugin directory already exists, update it instead of cloning again:

```bash
git -C "$HOME/.zsh/plugins/zsh-autosuggestions" pull --ff-only
git -C "$HOME/.zsh/plugins/zsh-syntax-highlighting" pull --ff-only
git -C "$HOME/.zsh/plugins/zsh-autocomplete" pull --ff-only
```

## 6. Update `~/.zshrc`

`zsh-autocomplete` should be sourced near the beginning of `.zshrc`, before any `compdef` usage and before manual `compinit` calls. If your `.zshrc` already contains these lines, remove or comment them when enabling `zsh-autocomplete`:

```zsh
autoload -Uz compinit
compinit
```

On Ubuntu systems, `zsh-autocomplete` may require this line in `~/.zshenv` to prevent global completion initialization from running first:

```bash
grep -qxF 'skip_global_compinit=1' "$HOME/.zshenv" 2>/dev/null || printf '\n%s\n' 'skip_global_compinit=1' >> "$HOME/.zshenv"
```

Add this block to `~/.zshrc` after your environment variables and before other plugins that may override keybindings. Keep syntax highlighting last.

```zsh
# --- USER STARSHIP & ZSH PLUGINS SETUP ---

export PATH="$HOME/.local/bin:$PATH"

# zsh-autocomplete: load early, before compdef/manual compinit usage.
if [ -r "$HOME/.zsh/plugins/zsh-autocomplete/zsh-autocomplete.plugin.zsh" ]; then
  source "$HOME/.zsh/plugins/zsh-autocomplete/zsh-autocomplete.plugin.zsh"
fi

# Starship prompt initialization.
if [ -x "$HOME/.local/bin/starship" ]; then
  eval "$("$HOME/.local/bin/starship" init zsh)"
elif command -v starship >/dev/null 2>&1; then
  eval "$(starship init zsh)"
fi

# Optional history autosuggestions. If keybindings change after enabling
# zsh-autocomplete, keep this after zsh-autocomplete and reapply bindkey here.
if [ -r "$HOME/.zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" ]; then
  source "$HOME/.zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh"
  bindkey '^f' forward-word
fi

# Syntax highlighting must be loaded last.
if [ -r "$HOME/.zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" ]; then
  source "$HOME/.zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh"
fi
```

## 7. Verify setup

```bash
command -v starship
starship --version
starship print-config >/dev/null && echo "Starship config OK"
zsh -n "$HOME/.zshrc"
exec zsh
```

Expected behavior: Starship loads with the configured prompt, syntax highlighting works, and autocomplete behavior is available in the new Zsh session.

## Troubleshooting

- If `starship` is not found, confirm the Starship binary location is on `PATH` before Starship initialization in `.zshrc`.
- If completion behaves strangely after enabling `zsh-autocomplete`, make sure it is loaded before `compdef` and before manual `compinit` calls.
- If syntax highlighting does not work, confirm `zsh-syntax-highlighting.zsh` is sourced after every other plugin.
- If `Ctrl+F` does not accept suggestions, keep `bindkey '^f' forward-word` after loading `zsh-autosuggestions`.
- If the prompt color does not change after editing `~/.config/starship.toml`, start a new Zsh session with `exec zsh`.
