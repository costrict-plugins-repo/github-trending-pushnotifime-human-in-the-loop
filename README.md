# PushNotifi — Claude Code plugin

Claude Code plugin that bundles the [PushNotifi.me](https://pushnotifi.me) MCP server (human-in-the-loop approvals on your phone), skills for scaffolding and recipes, and slash commands for Node project bootstrap.

## Install

### From this repository’s marketplace (Git clone)

This repo includes a [plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) at `.claude-plugin/marketplace.json`. Relative plugin sources only resolve when the marketplace is added **via Git** (not via a raw URL that downloads only `marketplace.json`). After cloning:

```text
/plugin marketplace add /path/to/claude-plugin-pushnotifi
/plugin install pushnotifi@pushnotifi-me
```

Or with the CLI:

```bash
claude plugin marketplace add /path/to/claude-plugin-pushnotifi
claude plugin install pushnotifi@pushnotifi-me
```

Validate the plugin and marketplace from this repository’s root:

```bash
cd /path/to/claude-plugin-pushnotifi
claude plugin validate .
claude plugin validate .claude-plugin/plugin.json
```

Then enable the plugin in `/plugin` and complete the credential prompts (API key from the [dashboard](https://pushnotifi.me/dashboard)).

### Local development (no marketplace)

```bash
claude --plugin-dir /path/to/claude-plugin-pushnotifi
```

### Team discovery (`extraKnownMarketplaces`)

To suggest the marketplace when people trust the project folder, add to `.claude/settings.json` (see [Plugin settings](https://code.claude.com/docs/en/settings#plugin-settings)):

```json
{
  "extraKnownMarketplaces": {
    "pushnotifi-me": {
      "source": {
        "source": "github",
        "repo": "YOUR_ORG/claude-plugin-pushnotifi"
      }
    }
  }
}
```

Adjust `repo` to your canonical GitHub `owner/name`. Pin with `ref` or `sha` on the source object if you need stable channels.

## Layout

| Path | Role |
| ---- | ---- |
| `.claude-plugin/plugin.json` | Manifest, `userConfig` for API keys |
| `.mcp.json` | MCP server: `node` + `mcp/dist/server.js`, `NODE_PATH` under `${CLAUDE_PLUGIN_DATA}` |
| `hooks/hooks.json` | `SessionStart`: installs MCP production dependencies into `${CLAUDE_PLUGIN_DATA}` when `mcp/package.json` changes |
| `mcp/` | TypeScript MCP server (same implementation as the Cursor plugin) |
| `skills/` | Scaffolding, recipes, approval workflow |
| `commands/` | `/pushnotifi:init`, `/pushnotifi:test` |

## Develop

After editing `mcp/src`:

```bash
cd mcp && npm install && npm run build
```

Use `claude plugin validate` as in **Install** after changing manifests, skills, commands, or hooks.

## Version

Aligned with the Cursor plugin MCP server: **0.4.0**.
