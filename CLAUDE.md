# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
# Build registry (validates all agents and outputs to dist/)
uv run --with jsonschema .github/workflows/build_registry.py

# Build without schema validation (if jsonschema not available)
python .github/workflows/build_registry.py
```

## Architecture

This is a registry of ACP (Agent Client Protocol) agents. The structure is:

```
<agent-id>/
├── agent.json    # Agent metadata and distribution info (required)
└── icon.svg      # Agent icon, must be 16x16 (optional)
```

**Build process** (`.github/workflows/build_registry.py`):
1. Scans directories for `agent.json` files
2. Validates against `agent.schema.json` (JSON Schema)
3. Validates icon dimensions (16x16 SVG required)
4. Aggregates all agents into `dist/registry.json`
5. Copies icons to `dist/<agent-id>.svg`

**CI/CD** (`.github/workflows/build-registry.yml`):
- PRs: Runs validation only
- Push to main: Validates, then publishes versioned + `latest` GitHub releases

## Validation Rules

- `id`: lowercase, hyphens only, must match directory name
- `version`: semantic versioning (e.g., `1.0.0`)
- `distribution`: at least one of `binary`, `npx`, `bunx`, `uvx`
- `icon.svg`: must be 16x16 (warnings for non-compliance)

## Distribution Types

- `binary`: Platform-specific archives (`darwin-aarch64`, `linux-x86_64`, etc.)
- `npx`/`bunx`: npm packages
- `uvx`: PyPI packages
