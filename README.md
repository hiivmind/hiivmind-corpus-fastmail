# Fastmail Documentation Corpus

A Claude Code plugin providing always-current access to Fastmail documentation with intelligent navigation.

## Features

- **Indexed Navigation**: Structured index of all Fastmail documentation with summaries, key concepts, and cross-references
- **Automatic Updates**: Incremental index updates from upstream documentation changes
- **Smart Matching**: Finds relevant docs based on concepts, summaries, and section headings

## Installation

### As a Plugin

```bash
# Add the marketplace containing this plugin
/plugin marketplace add hiivmind/hiivmind-corpus-fastmail

# Install the plugin
/plugin install hiivmind-corpus-fastmail

# Restart Claude Code
```

### Manual Installation

Clone this repository to your Claude Code plugins directory.

## First-Time Setup

After installing, use the `hiivmind-corpus-build` skill to build the index:

```
Please build the Fastmail documentation index
```

This requires the `hiivmind-corpus` meta-plugin to be installed.

## Maintenance Skills

This corpus is managed by **hiivmind-corpus** skills:

| Skill | Purpose |
|-------|---------|
| `hiivmind-corpus-add-source` | Add new documentation sources |
| `hiivmind-corpus-build` | Build/rebuild the documentation index |
| `hiivmind-corpus-enhance` | Deepen coverage on specific topics |
| `hiivmind-corpus-refresh` | Update index from upstream changes |

## Usage

### Automatic Navigation

The navigation skill is model-invoked. Simply ask questions about Fastmail:

```
How do I authenticate with the Fastmail API?
What are the JMAP core methods?
How do I set up email forwarding via API?
```

Claude will automatically find and cite relevant documentation.

### Index Updates

Refresh from upstream changes (uses `hiivmind-corpus-refresh`):

```
Refresh the Fastmail corpus from upstream
```

Full rebuild (uses `hiivmind-corpus-build`):

```
Rebuild the Fastmail documentation index from scratch
```

Enhance a specific topic (uses `hiivmind-corpus-enhance`):

```
Add more detail about authentication to the Fastmail index
```

## Skills

### hiivmind-corpus-fastmail-navigate

Finds relevant documentation for Fastmail-related coding tasks. Automatically invoked when you ask about:

- Fastmail API usage
- JMAP protocol specifications
- Email automation patterns
- Mail server integration
- API authentication and authorization

## File Structure

```
hiivmind-corpus-fastmail/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── commands/
│   └── navigate.md           # Navigate command (explicit entry point)
├── skills/
│   └── navigate/
│       └── SKILL.md          # Documentation navigation (auto-invoked)
├── data/
│   ├── config.yaml           # Source definitions + settings
│   ├── index.md              # Generated documentation index
│   └── uploads/              # Local document uploads
├── references/
│   └── project-awareness.md  # CLAUDE.md snippet for projects
├── .source/                  # Cloned docs repos (gitignored)
├── .cache/                   # Cached web content (gitignored)
└── README.md
```

## Configuration

The `data/config.yaml` and `data/index.md` files are managed by hiivmind-corpus skills. Do not edit them manually.

To add new sources, use `hiivmind-corpus-add-source`. To rebuild or enhance the index, use `hiivmind-corpus-build` or `hiivmind-corpus-enhance`.

## Requirements

- `hiivmind-corpus` meta-plugin installed (provides maintenance skills)
- Git (for cloning and updating documentation)
- Claude Code with plugin support

## License

MIT
