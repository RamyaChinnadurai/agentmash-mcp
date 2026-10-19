# Install AgentMash MCP server (instructions for AI coding agents)

AgentMash is a remote MCP server (Streamable HTTP) that gives you read-only access to the user's bookmark library. Follow these steps in order.

## 1. Get the user's API key

Ask the user for their AgentMash API key. Tell them to create one in AgentMash: open the **Agents & API** page and click **Create API key**.

Rules:
- The key starts with `am_live_`.
- Never invent, guess or reuse a key. If the user has none, stop and ask them to create one.
- Never print the key in logs, summaries or commit messages. Only write it into the user's local MCP config.
- Do not commit the config file to version control.

## 2. Add the server to the user's client

Server details:
- URL: `https://agentmash.ai/mcp`
- Transport: Streamable HTTP
- Auth header: `Authorization: Bearer <the user's key>`
- No OAuth. If the client cannot set headers, use `https://agentmash.ai/mcp?token=<the user's key>` as the URL.

### Generic config (clients with remote HTTP support)

```
URL   https://agentmash.ai/mcp
Auth  Authorization: Bearer am_live_YOUR_KEY
```

As JSON, for clients that use an `mcpServers` object:

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

Replace `am_live_YOUR_KEY` with the key the user gave you.

### Stdio-only clients (mcp-remote bridge)

If the client only supports local (stdio) servers, use the `mcp-remote` bridge. It needs Node with `npx` installed.

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

### Other clients

- Claude Code: `claude mcp add --transport http agentmash https://agentmash.ai/mcp --header "Authorization: Bearer am_live_YOUR_KEY"`
- Cursor: use the JSON above in `~/.cursor/mcp.json`.
- Codex: add to `~/.codex/config.toml`:

```
[mcp_servers.agentmash]
url = "https://agentmash.ai/mcp"
bearer_token = "am_live_YOUR_KEY"
```

Restart or reload the client if the tools do not appear.

## 3. Verify

Call the `list_sources` tool. It takes no arguments and is read-only.

- Success: a response listing the user's platforms and saves per platform. An empty list is fine. It means the user has not clicked Sync in the AgentMash Chrome extension yet.
- "Unauthorized": the key is missing, wrong or cut short. Ask the user to copy it again, or create a new key.

## 4. Use it

Available tools, all read-only: `search_saves`, `get_save`, `recent_saves`, `list_sources`, `list_collections`, `get_collection_saves`. Use `search_saves` first, then `get_save` for the full text, and cite each save with its link.

Help: hello@agentmash.ai
