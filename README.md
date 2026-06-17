# poc-claude-codex-bridge

Bridge utilities for integrating Claude CLI with Codex proxy infrastructure.

# WARNING

It is currently slow for models other than gpt-5.5 (which is the highest consuming model).

## Overview

This repository contains wrapper scripts that enable seamless integration between Claude CLI and a local Codex proxy server. The proxy allows routing Anthropic API calls through Codex's OpenAI-compatible endpoint.

## Components

### `bin/codex-claude-proxy`

Starts the Codex-to-Claude proxy server that translates Codex API requests to Claude-compatible format.

**Usage:**
```bash
codex-claude-proxy [mode]

# Modes:
#   safe     - Workspace-write only (default)
#   full     - Full network and system access (use with caution)
#   readonly - Read-only access
```

**Environment Variables:**
- `CODEX_CLAUDE_HOST` - Proxy host (default: 127.0.0.1)
- `CODEX_CLAUDE_PORT` - Proxy port (default: 4100)
- `CODEX_CLAUDE_AUTH_TOKEN` - Authentication token
- `CODEX_CLAUDE_MODEL_*` - Model mappings for Haiku, Sonnet, Fable, Opus
- `CODEX_CLAUDE_TIMEOUT` - Request timeout in seconds

### `bin/claude-codex`

Wrapper script that configures Claude CLI to route requests through the local Codex proxy.

**Features:**
- Automatic model name translation (Claude → Codex)
- Smart routing based on model capabilities
- Transparent API key handling

**Mapped Models:**
| Claude | Codex | Description |
|--------|-------|-------------|
| haiku  | gpt-5.4-mini | Fast, high-throughput |
| sonnet | gpt-5.4 | Balanced performance |
| fable  | gpt-5.4 | Creative/general |
| opus   | gpt-5.5 | Most capable |
| codex-coding | gpt-5.3-codex | Code-specialized |

## Installation

1. Copy wrapper scripts to your PATH:
   ```bash
   cp bin/* ~/.local/bin/
   chmod +x ~/.local/bin/codex-claude-proxy ~/.local/bin/claude-codex
   ```

2. Start the proxy:
   ```bash
   codex-claude-proxy safe
   ```

3. Use Claude CLI through the proxy:
   ```bash
   claude-codex "Your prompt here"
   ```

## Architecture

```
┌─────────────┐     ┌───────────────────┐     ┌─────────────┐
│ Claude CLI  │────▶│ codex-claude-proxy│────▶│  Codex API  │
│             │     │  (localhost:4100) │     │             │
└─────────────┘     └───────────────────┘     └─────────────┘
```

## License

MIT
