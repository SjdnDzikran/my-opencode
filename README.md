# My OpenCode Configuration

Personal OpenCode configuration including skills, tools, and custom settings.

## Structure

```
my-opencode/
├── opencode/
│   ├── opencode.json          # Main OpenCode config (without sensitive keys)
│   └── skill/
│       └── vps-security-hardening/
│           └── SKILL.md       # VPS security hardening skill
├── README.md
└── LICENSE
```

## Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/my-opencode.git
   cd my-opencode
   ```

2. Copy configuration to OpenCode config directory:
   ```bash
   cp -r opencode/* ~/.config/opencode/
   ```

3. **Important**: Add your MCP API keys to `~/.config/opencode/opencode.json`:
   ```json
   {
     "$schema": "https://opencode.ai/config.json",
     "mcp": {
       "web-reader": {
         "type": "remote",
         "url": "https://api.z.ai/api/mcp/web_reader/mcp",
         "headers": {
           "Authorization": "Bearer YOUR_API_KEY_HERE"
         }
       }
     }
   }
   ```

## Skills

### vps-security-hardening

Comprehensive VPS security hardening for Linux servers:
- UFW firewall configuration
- SSH hardening (key-based auth)
- Fail2ban setup for brute force protection

To use this skill in OpenCode:
```bash
skill({ name: "vps-security-hardening" })
```

## Security

**API keys are not included in this repository.** You must add your own MCP API keys after setup.

The `opencode.json` file in this repo is a template without sensitive credentials.

## License

MIT License - feel free to use this configuration as a starting point for your own OpenCode setup.