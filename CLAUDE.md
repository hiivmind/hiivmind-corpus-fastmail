# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a **hiivmind-corpus documentation corpus** for Fastmail. It was created by the `hiivmind-corpus` meta-plugin and provides always-current access to Fastmail documentation with intelligent navigation.

## IMPORTANT: Maintenance Skills

This corpus is managed by the **hiivmind-corpus** plugin system. **Do not manually edit** `data/index.md` or `data/config.yaml` - use these skills instead:

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `hiivmind-corpus-add-source` | Add new documentation sources | Adding git repos, web pages, or local files |
| `hiivmind-corpus-build` | Build/rebuild the documentation index | After init, or for full rebuilds |
| `hiivmind-corpus-enhance` | Deepen coverage on specific topics | When more detail is needed on a topic |
| `hiivmind-corpus-refresh` | Update index from upstream changes | When source docs have been updated |

### Example Usage

```
# Add a new source
"Add the fastmail examples repo as a source"

# Build or rebuild the index
"Build the Fastmail documentation index"

# Enhance a topic
"Enhance the index with more detail on authentication"

# Refresh from upstream
"Refresh the Fastmail corpus from upstream"
```

## Directory Structure

```
hiivmind-corpus-fastmail/
├── .claude-plugin/
│   └── plugin.json               # Plugin manifest
├── commands/
│   └── navigate.md               # Navigate command (explicit entry point)
├── skills/
│   └── navigate/
│       └── SKILL.md              # Navigation skill (auto-invoked for Fastmail questions)
├── data/
│   ├── config.yaml               # Source definitions and tracking (managed by skills)
│   ├── index.md                  # Documentation index (managed by skills)
│   └── uploads/                  # Local document uploads
├── references/
│   └── project-awareness.md      # CLAUDE.md snippet for projects
├── .source/                      # Cloned git sources (gitignored)
├── .cache/                       # Cached web content (gitignored)
├── .gitignore
└── README.md
```

## Key Files

| File | Purpose | Editable? |
|------|---------|-----------|
| `data/config.yaml` | Source definitions, commit tracking | **No** - use `hiivmind-corpus-add-source` |
| `data/index.md` | Documentation index with summaries | **No** - use `hiivmind-corpus-build/enhance/refresh` |
| `skills/navigate/SKILL.md` | Navigation skill instructions | Yes, for customization |
| `commands/navigate.md` | Navigate command instructions | Yes, for customization |
| `.claude-plugin/plugin.json` | Plugin metadata | Yes, for metadata updates |

## How Navigation Works

When you ask questions about Fastmail, the navigate skill:

1. Reads `data/index.md` to find relevant file paths
2. Fetches content from `.cache/web/{source_id}/{path}` (web sources) or `.source/{source_id}/{path}` (git sources)
3. Cites sources and suggests related documentation

## Source Types

This corpus can include multiple source types:

- **git**: Cloned repos in `.source/{source_id}/` - use `hiivmind-corpus-add-source` to add
- **web**: Cached pages in `.cache/web/{source_id}/` - use `hiivmind-corpus-add-source` to add
- **local**: Uploaded files in `data/uploads/{source_id}/` - use `hiivmind-corpus-add-source` to add

## Upstream Tracking

The corpus tracks upstream commits in `data/config.yaml`. Run `hiivmind-corpus-refresh` periodically to:
- Detect upstream documentation changes
- Update the index based on diffs
- Maintain accurate documentation coverage

## Requirements

- `hiivmind-corpus` plugin must be installed (provides maintenance skills)
- Git (for source cloning and updates)
