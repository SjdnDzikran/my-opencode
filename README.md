# My Development Configurations

Personal development environment configurations including OpenCode, ZSH, and other tools.

## Structure

```
my-configs/
├── opencode/
│   ├── opencode.json          # Main OpenCode config (without sensitive keys)
│   └── skill/
│       └── vps-security-hardening/
│           └── SKILL.md       # VPS security hardening skill
├── zsh/
│   ├── .zshrc                # ZSH configuration with Oh My Zsh
│   └── custom/
│       ├── themes/             # Custom themes
│       └── plugins/           # Custom plugins
├── README.md
└── LICENSE
```

## Setup

### Complete Setup (All Configs)

Clone and setup all configurations:

```bash
git clone https://github.com/SjdnDzikran/my-configs.git
cd my-configs
```

#### OpenCode Setup

```bash
cp -r opencode/* ~/.config/opencode/

# Important: Add your MCP API keys to ~/.config/opencode/opencode.json
# Replace YOUR_API_KEY_HERE placeholders with your actual keys
```

#### ZSH Setup (Oh My Zsh)

```bash
# Backup existing config
cp ~/.zshrc ~/.zshrc.backup 2>/dev/null

# Copy new config
cp zsh/.zshrc ~/.zshrc

# Copy custom themes and plugins
cp -r zsh/custom/* ~/.oh-my-zsh/custom/

# Reload ZSH
source ~/.zshrc
```

## Configurations

### OpenCode

- **File**: `opencode/opencode.json`
- **Purpose**: OpenCode AI assistant configuration
- **Features**: MCP plugins, keybinds, model selection
- **Skills**: VPS security hardening skill included

#### Skills

##### vps-security-hardening

Comprehensive VPS security hardening for Linux servers:
- UFW firewall configuration
- SSH hardening (key-based auth)
- Fail2ban setup for brute force protection

To use this skill in OpenCode:
```bash
skill({ name: "vps-security-hardening" })
```

### ZSH (Oh My Zsh)

- **File**: `zsh/.zshrc`
- **Theme**: af-magic
- **Features**: 
  - Auto CD (type directory name to change)
  - OpenCode path integration
  - Oh My Zsh with custom theme
- **Customizations**: Custom themes and plugins

#### Custom Components

- **Themes**: Located in `zsh/custom/themes/`
- **Plugins**: Located in `zsh/custom/plugins/`

## Security

**API keys are not included in this repository.** You must add your own MCP API keys after setup.

The `opencode.json` file in this repo is a template without sensitive credentials.

## Features

- **OpenCode Config**: AI-powered development assistant with MCP plugins
- **ZSH Customization**: Optimized shell with Oh My Zsh and custom theme
- **VPS Security Skill**: Automated VPS hardening for production servers
- **Unified Setup**: One repository for all development configurations

## License

MIT License - feel free to use these configurations as a starting point for your own setup.

## Contributing

Feel free to fork, modify, and improve these configurations for your needs!

## Usage

After setup, you'll have:
1. **OpenCode** ready with MCP plugins and custom skills
2. **ZSH** with optimized configuration and custom theme
3. **VPS Security** skill available in OpenCode for server hardening

## Maintenance

To update configurations after initial setup:

```bash
cd my-configs
git pull

# Update individual configs as needed
cp opencode/opencode.json ~/.config/opencode/
cp zsh/.zshrc ~/
cp -r zsh/custom/* ~/.oh-my-zsh/custom/
```