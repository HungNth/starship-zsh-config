# Starship Config

A personal [Starship](https://starship.rs/) prompt configuration for a clean, information-rich terminal experience across macOS, Linux, and Windows.

This repository includes a Catppuccin Macchiato-inspired `starship.toml` with OS, username, directory, Git, language/runtime, Conda, command-duration, and multiline prompt segments.

> [!NOTE]
> The prompt uses Nerd Font icons. For the best result, install a Nerd Font and select it in your terminal profile.

## What's included

```text
starship-config/
├── README.md
├── README.macos-linux.md
├── README.windows.md
└── starship.toml
```

| File | Description |
| --- | --- |
| `starship.toml` | Main Starship prompt configuration. |
| `README.macos-linux.md` | macOS/Linux setup guide for Starship and Zsh plugins. |
| `README.windows.md` | Windows setup guide for PowerShell 7. |

## Quick start

Clone the repository:

```bash
git clone https://github.com/HungNth/starship-config.git
cd starship-config
```

Then follow the guide for your shell and operating system:

- [macOS/Linux Zsh setup](./README.macos-linux.md)
- [Windows PowerShell setup](./README.windows.md)

For Starship's official installation and configuration documentation, see the [Starship guide](https://starship.rs/guide/).

## Prompt highlights

- Powerline-style prompt layout with Catppuccin Macchiato colors.
- OS and username segment shown consistently across platforms.
- Directory truncation with friendly folder substitutions.
- Git branch and status indicators.
- Runtime indicators for Node.js, Bun, C, Rust, Go, PHP, Java, Kotlin, Haskell, Python, and Conda.
- Command duration display with long-running command notifications.
- Multiline prompt with distinct success, error, Vim, and visual-mode symbols.

## Using this config

Starship loads the config from the path in `STARSHIP_CONFIG`, or from the default config path for your platform. The OS-specific guides show both approaches:

- macOS/Linux: copy `starship.toml` to `~/.config/starship.toml` and initialize Starship in `~/.zshrc`.
- Windows: set `$ENV:STARSHIP_CONFIG` in your PowerShell 7 profile and initialize Starship with `Invoke-Expression (&starship init powershell)`.
