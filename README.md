Omarchy shell configuration for Zsh shell.

## Architecture

Shared shell config (aliases, functions, environment, init) is pulled from [omadots](https://github.com/omacom-io/omadots) at package build time. This repo only contains the zsh-specific bits. Config is sourced directly from `/usr/share/` so package updates take effect immediately.

```mermaid
graph TD
    subgraph "Package Build"
        omadots["omadots/config/shell/
        all, aliases, functions,
        envs, inits, inputrc"]
        zsh_repo["omarchy-zsh/
        zoptions, templates, bin"]
        omadots --> pkg["/usr/share/omarchy-zsh/shell/"]
        zsh_repo --> pkg
    end

    subgraph "omarchy-setup-zsh (one time)"
        zsh_repo -->|"cp templates/"| dotfiles["~/.zshrc + ~/.bashrc"]
        pkg -->|"cp inputrc"| inputrc["~/.inputrc"]
    end

    subgraph "Runtime"
        bash["bash starts"] --> bashrc["~/.bashrc"]
        bashrc -->|"exec zsh"| zshrc["~/.zshrc"]
        zshrc -->|source| zoptions["/usr/share/omarchy-zsh/shell/zoptions
        (zsh-only: completion, keybinds, fzf widgets)"]
        zshrc -->|source| all["/usr/share/omarchy-zsh/shell/all"]
        all --> envs["envs"]
        all --> aliases["aliases"]
        all --> functions["functions"]
        all --> inits["inits
        (detects bash vs zsh at runtime)"]
    end

    style omadots fill:#4a9,stroke:#333,color:#fff
    style zsh_repo fill:#68f,stroke:#333,color:#fff
    style zoptions fill:#68f,stroke:#333,color:#fff
    style envs fill:#4a9,stroke:#333,color:#fff
    style aliases fill:#4a9,stroke:#333,color:#fff
    style functions fill:#4a9,stroke:#333,color:#fff
    style inits fill:#4a9,stroke:#333,color:#fff
```

Green = shared with bash (from omadots), Blue = zsh-specific (from this repo).

## Install

```bash
# Install the package
sudo pacman -S omarchy-zsh

# Setup zsh (optional: auto-launch from bash)
omarchy-setup-zsh
```

## fzf Keybindings

- **Ctrl+Alt+F** - Search files/directories
- **Ctrl+Alt+L** - Search Git Log
- **Ctrl+R** - Search command history
- **Ctrl+T** - Search files in current directory
- **Ctrl+V** - Search Variables
- **Alt+C** - cd into selected directory

## Customization

To add your own configuration or override defaults:

```bash
# Edit your .zshrc
nvim ~/.zshrc

# Add customizations at the bottom after the omarchy-zsh loading
```

User customizations in `~/.zshrc` take precedence over system defaults.

## Uninstall

```bash
sudo pacman -R omarchy-zsh
```

To restore bash, copy a backup to `~/.bashrc` (backups are saved as `.bashrc.backup-*`).
