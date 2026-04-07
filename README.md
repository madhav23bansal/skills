# Claude Code Skills Marketplace

A collection of DevOps and server management plugins for [Claude Code](https://claude.ai/code).

## Installation

```bash
# Add this repo as a marketplace
/plugin marketplace add madhav23bansal/skills

# Install a plugin
/plugin install server-setup@madhav23bansal-skills
```

## Available Plugins

| Plugin | Command | Description |
|--------|---------|-------------|
| [server-setup](plugins/server-setup/) | `/server-setup root@IP` | Interactive production server hardening for Ubuntu VPS |

## Adding a New Plugin

1. Create a directory under `plugins/`: `plugins/my-plugin/`
2. Add `.claude-plugin/plugin.json` with name, version, description
3. Add `skills/my-skill/SKILL.md` with instructions
4. Add a `README.md` for the plugin
5. Update `plugins/README.md` index

```
plugins/
└── my-plugin/
    ├── .claude-plugin/
    │   └── plugin.json
    ├── README.md
    └── skills/
        └── my-skill/
            ├── SKILL.md
            └── phases/       # optional supporting files
```

## License

MIT
