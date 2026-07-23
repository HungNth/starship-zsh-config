# Windows PowerShell Setup

This guide configures Starship for PowerShell 7 on Windows using the `starship.toml` file from this repository.

## 1. Install Starship

Install Starship with Windows Package Manager:

```powershell
winget install --id Starship.Starship
```

For more installation options and shell-specific details, see the [official Starship guide](https://starship.rs/guide/).

> [!NOTE]
> This prompt uses Nerd Font symbols. If icons render as squares or missing glyphs, install a Nerd Font and select it in your terminal profile.

## 2. Clone this repository

```powershell
git clone https://github.com/HungNth/starship-config.git
cd starship-config
```

The examples below use this repository path:

```text
D:\Code\Apps\starship-config\starship.toml
```

If you cloned the repository somewhere else, replace that path with the full path to your local `starship.toml` file.

## 3. Open your PowerShell 7 profile

Open the profile file with Notepad:

```powershell
notepad $PROFILE
```

Or open it with Visual Studio Code:

```powershell
code $PROFILE
```

If the profile file does not exist yet, create it first:

```powershell
if (-not (Test-Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force | Out-Null
}
```

## 4. Configure Starship in `$PROFILE`

Add this line near the top of the profile so Starship uses the config from this repository:

```powershell
$ENV:STARSHIP_CONFIG = "D:\Code\Apps\starship-config\starship.toml"
```

Add this line near the end of the profile to initialize Starship:

```powershell
Invoke-Expression (&starship init powershell)
```

A minimal PowerShell 7 profile can look like this:

```powershell
# Starship config path.
$ENV:STARSHIP_CONFIG = "D:\Code\Apps\starship-config\starship.toml"

# Other PowerShell settings can stay here.

# Starship prompt initialization.
Invoke-Expression (&starship init powershell)
```

## 5. Reload PowerShell

Reload the current session:

```powershell
. $PROFILE
```

Or close the terminal and open it again.

## 6. Verify setup

```powershell
starship --version
starship print-config
```

Expected behavior: PowerShell loads with the configured Starship prompt.

## Troubleshooting

- If `starship` is not recognized, close and reopen PowerShell after installing Starship, then run `starship --version` again.
- If the prompt does not use this repository's theme, confirm `$ENV:STARSHIP_CONFIG` points to the correct `starship.toml` path.
- If icons are missing, install a Nerd Font and configure Windows Terminal, VS Code, or your terminal emulator to use it.
