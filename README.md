# AgentMash MCP server

All your bookmarks in one library. Share them with your AI agents.

This repo documents the hosted AgentMash MCP server: how to connect your AI agent to your AgentMash library.

## What AgentMash does

- The AgentMash Chrome extension imports your saves from X, LinkedIn, Instagram and more when you click Sync. Nothing runs in the background.
- Everything lands in one library.
- Your AI agents read that library over MCP, with read-only tools.

## Server

- URL: `https://agentmash.ai/mcp`
- Transport: Streamable HTTP

## Authentication

AgentMash uses an API key, not OAuth.

1. In AgentMash, open the **Agents & API** page and click **Create API key**.
2. Copy the key right away. It starts with `am_live_` and is shown only once.
3. Send it as a header:

```
Authorization: Bearer am_live_YOUR_KEY
```

For clients that cannot set headers, append `?token=am_live_YOUR_KEY` to the URL instead.

## Setup

Replace `am_live_YOUR_KEY` with your own key in each snippet.

### Claude Code

```
claude mcp add --transport http agentmash https://agentmash.ai/mcp --header "Authorization: Bearer am_live_YOUR_KEY"
```

### Claude Desktop

Claude Desktop connects through the `mcp-remote` bridge, so you need Node (with `npx`) installed. Add this to `claude_desktop_config.json`:

```
{
  "mcpServers": {
    "agentmash": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://agentmash.ai/mcp",
        "--header",
        "Authorization: Bearer am_live_YOUR_KEY"
      ]
    }
  }
}
```

### Cursor

Add this to `~/.cursor/mcp.json`:

```
{
  "mcpServers": {
    "agentmash": {
      "url": "https://agentmash.ai/mcp",
      "headers": {
        "Authorization": "Bearer am_live_YOUR_KEY"
      }
    }
  }
}
```

### Cline

In Cline, open **MCP Servers**, then **Configure**, then **Configure MCP Servers**, and add this to `cline_mcp_settings.json`. Set `"type": "streamableHttp"`: without it Cline falls back to the legacy SSE transport and will not connect.

```
{
  "mcpServers": {
    "agentmash": {
      "type": "streamableHttp",
      "url": "https://agentmash.ai/mcp",
      "headers": {
        "Authorization": "Bearer am_live_YOUR_KEY"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Or with the Cline CLI:

```
cline mcp add agentmash https://agentmash.ai/mcp --transport http --header "Authorization: Bearer am_live_YOUR_KEY" --yes
```

### Codex

Add this to `~/.codex/config.toml`:

```
[mcp_servers.agentmash]
url = "https://agentmash.ai/mcp"
bearer_token_env_var = "AGENTMASH_API_KEY"
```

Then set the key in your shell before starting Codex: `export AGENTMASH_API_KEY=am_live_YOUR_KEY`. Codex reads the bearer token from that environment variable.

## Tools

All six tools are read-only.

| Tool | What it does |
|---|---|
| `search_saves` | Searches your saves. Leave the query out to list your newest saves first. |
| `get_save` | Gets one save in full: text, stats, links, media and collections. |
| `recent_saves` | Lists saves from the last few days. |
| `list_sources` | Lists the platforms in your library, with saves per platform and last sync. |
| `list_collections` | Lists your collections with their names, platform and number of saves. |
| `get_collection_saves` | Gets the saves inside one collection. |

## Revoke a key

Open the **Agents & API** page in AgentMash and click **Revoke** on the key. Agents using that key stop working within about a minute.

## Links

- AgentMash: https://agentmash.ai
- MCP setup guide: https://agentmash.ai/mcp-setup


## About this repo

The AgentMash app source code is not in this repo. This repo only documents the hosted MCP server.

For AI coding agents, see [llms-install.md](llms-install.md).

## Support

Email hello@agentmash.ai.
